# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Caml Light 0.82, a revival of INRIA's small bytecode implementation of the Caml language (an ML-family functional language, ancestor of OCaml). It is a self-hosting compiler: the compiler, linker, librarian, lexer-generator and standard library are written in Caml Light, and run on a bytecode virtual machine written in C. The revival's main change is making decades-old K&R-era C compile under modern clang/gcc.

Everything below assumes the working tree under `sources/src/`. The parallel `windows/` tree is the legacy MS-DOS/Windows port and is not built by the Unix flow.

## Build, bootstrap, test

All commands run from `sources/src/`:

```bash
make configure   # probes the host, writes sources/config/m.h and s.h
make OPTS="-O2 -Wno-implicit-int -Wno-deprecated-non-prototype -Wno-pointer-sign -Wno-implicit-function-declaration" world
make OPTS="...same flags..." bootstrap   # self-recompile to a byte-exact fixpoint
sudo make install                        # to /usr/local (BINDIR, LIBDIR overridable)
make clean
make depend                              # regenerate Makefile dependency stanzas
```

The `OPTS` warning-suppression flags are not optional on a current compiler: the C sources predate ANSI prototypes and signed-char/pointer-sign strictness, so without these flags the runtime fails to build. CI (`.github/workflows/ci.yml`, ubuntu-latest + macos-latest) runs exactly `configure` then `world` then `bootstrap` with these flags, then a toplevel smoke test. Keep the README CI badge and the workflow green.

`make world` builds subsystems in dependency order (runtime, yacc, lib, compiler, linker, librar, lex, toplevel, launch) and finishes with a built-in sanity check that `fib 20` evaluates to 10946. There is no unit-test suite; correctness is established by two things: the `fib` smoke test and, more importantly, the bootstrap fixpoint.

### The bootstrap fixpoint is the real test

`make bootstrap` = `backup` then `promote` then `again` then `compare`. It copies the freshly built `compiler/camlcomp` (and linker/librar/lex) up to the top level, recompiles everything from clean with that new compiler, and then `cmp`s the result byte-for-byte against the previous build. Success means the compiler compiled by itself reproduces itself exactly. After any change to `compiler/`, a change is only trustworthy once bootstrap reports "successfully recompiled itself." A single non-fixpoint pass is normal; the Makefile tells you to run another cycle.

To run a single source file in the toplevel without installing (this is the smoke-test invocation, useful for quick checks):

```bash
echo "1 + 2;;" | ./camlrun toplevel/camltop -stdlib lib
```

## Component map (the bootstrap chain)

- `runtime/` (C) builds `camlrun`, the bytecode VM. `interp.c` is the ZINC abstract-machine interpreter; `instruct.h` defines the opcode set; minor/major GC, `extern.c`/`intern.c` (marshalling) and the C primitives live here.
- `compiler/` (Caml Light) builds `camlcomp`. Pipeline, readable from the `OBJS` order in its Makefile: lexer/parser produce `syntax`, then typing (`types`, `typing`, `ty_decl`, `ty_intf`), then `front` lowers to a lambda IR, `matching` compiles pattern matches, `back`/`instruct` lower lambda to bytecode, `emitcode`/`emit_phr` emit `.zo`.
- `linker/` builds `camllink`: links `.zo` objects into runnable bytecode.
- `librar/` builds `camllibr`: bundles `.zo` files into libraries.
- `lex/` builds `camllex` (lexer generator, like ocamllex); `yacc/` builds `camlyacc` (parser generator, a Berkeley-yacc port, written in C).
- `toplevel/` builds `camltop`, the interactive REPL.
- `lib/` is the standard library (`.ml`/`.mli`).
- `launch/` holds shell-script templates (`.tpl`) for the user-facing driver commands `camlc`, `camllight`, `camlmktop`, plus `camlexec.c`. These wrap `camlrun` + the bytecode tools.
- `tools/` has dev utilities: `camldep` (dependency generator used by `make depend`), `dumpobj`, `camlsize`, message-table checkers.
- `sources/contrib/` are optional add-on packages (libnum, libunix, libgraph, camltk4, debugger, profiler, ...), built separately via `sources/contrib/Makefile` after the core is installed. `sources/examples/` are sample programs.

### Committed bytecode binaries are the bootstrap seed

`sources/src/camlcomp`, `camllex`, `camllibr`, `camllink` are checked-in bytecode executables (binary `data`, not source). A compiler written in its own language cannot be built from nothing, so these seed the cycle: `make world` uses the top-level `camlcomp` to compile `compiler/` into a new one. When they appear modified in `git status`, that is a genuine rebuild of the compiler, not stray output. Commit a regenerated seed only deliberately, and only after bootstrap reaches its fixpoint.

## Conventions a newcomer will trip on

- **Module access is `module__name`, not `Module.name`.** Double underscore. Examples: `filename__check_suffix`, `arg__String`, `sys__catch_break`. `#open "modname";;` is the equivalent of `open`. This predates OCaml's dotted-module syntax.
- **File extensions**: `.ml` impl, `.mli` interface, `.zo` compiled bytecode object (OCaml's `.cmo`), `.zi` compiled interface (`.cmi`), `.mll` lexer, `.mly` grammar. `.mlp` is a `.ml` that must first pass through the C preprocessor `cpp` (hence the `CPP=` make variable) to produce a `.ml`; `.mlt` is a profiler temp. `.tpl` are shell-script templates expanded at install time.
- **Cross-language generated files. Do not hand-edit them.** `runtime/instruct.h` is the single source of truth for bytecode opcodes; `compiler/opcodes.ml`, `runtime/jumptbl.h`, and `runtime/opnames.h` are all generated from it (via `sed`/`awk`/`tools/make-opcodes`). The C primitive table `runtime/prims.c` is generated by scanning C functions tagged with a `/* ML */` comment. To add an opcode or a primitive, edit the C header / tag the C function and regenerate. All such generated files (and `config/m.h`, `config/s.h`, and the compiled `.zo`/`.zi`) are gitignored.
- **Host configuration is generated, not committed.** `make configure` runs `sources/config/autoconf`, which compiles probe programs (`config/auto-aux/`) to detect integer/pointer sizes, char signedness, endianness, and available libc functions, writing `config/m.h` (machine) and `config/s.h` (system). The runtime Makefile inlines these into the C sources with `sed`. Deleted artifacts like `config/auto-aux/hasgot.c` in `git status` are probe leftovers, now gitignored.

## House style note for this repo

Source comments and commit history are partly in French (the original INRIA authorship); recent revival commits mix French and English. Match the surrounding file. Do not introduce em-dashes or en-dashes in any prose, code, comment, or commit message; use ASCII punctuation.
