# Caml Light

[![CI](https://github.com/FranklinChen/camllight/actions/workflows/ci.yml/badge.svg)](https://github.com/FranklinChen/camllight/actions/workflows/ci.yml)

Caml Light is a small, portable implementation of the Caml language, a functional language from the ML family. Caml is close to Standard ML but not strictly conformant: there are some differences in syntax and semantics, and major differences in the module system. It is implemented as a fully bootstrapped bytecode compiler whose runtime and interpreter are written in standard C, so it is easy to port and small enough that it once ran comfortably on the personal computers of the early 1990s.

## About this fork

This repository (`FranklinChen/camllight`) is a **fork of [`camllight/camllight`](https://github.com/camllight/camllight)**, the upstream Caml Light repository. Upstream is the continuation of INRIA's original Caml Light: INRIA developed and released the system through version 0.75 (January 1999), and François Boisson later carried it forward to the 0.82 line that this fork builds.

The intent of this fork is deliberately narrow: **make the decades-old, K&R-era C of the runtime build and bootstrap cleanly under modern clang and gcc**, and add continuous integration so it stays that way. The changes are small and confined to making the C compile (function prototype and return-value fixes) plus minor cleanups; the Caml Light language, compiler, and standard library sources are unchanged from upstream. The needed warning-suppression flags are already the default in `sources/src/Makefile`, so the system builds out of the box (see [Building](#building)).

### What is new in this fork (not inherited from upstream)

These files were written for this fork and do **not** exist in [`camllight/camllight`](https://github.com/camllight/camllight):

- **`README.md`** (this file): a fork-specific overview, written from scratch here. It is not upstream's README, and it does not replace upstream's own documentation.
- **`.github/workflows/ci.yml`**: GitHub Actions CI that builds and bootstraps on Linux and macOS.
- **`CLAUDE.md`**: guidance for contributors (and AI assistants) working in this repository.

Everything under `sources/`, `doc/`, and `windows/` comes from upstream Caml Light.

## Original documentation

This README is not the authoritative documentation. The original Caml Light documentation is INRIA's:

- **Caml Light home page (INRIA):** <https://caml.inria.fr/caml-light/>
- **Caml Light reference manual and user's guide:** <https://caml.inria.fr/pub/docs/manual-caml-light/>

Within this repository, the upstream documentation is preserved at:

- `sources/README`: upstream's original README for the distribution.
- `doc/`: the reference manual sources, in several formats.
- `sources/COPYRIGHT` and `sources/LICENSE`: the original INRIA copyright and license.

## Features

- Bytecode compiler and interpreter
- Interactive toplevel system
- Batch compiler for standalone programs
- Lexer and parser generators (camllex and camlyacc)
- Standard library
- Various contributed libraries and tools

## Building

### Prerequisites

- A C compiler (gcc or clang)
- Make
- Unix-like environment

### Build Steps

1. Change to the `sources/src/` directory:

   ```bash
   cd sources/src
   ```

2. Edit the `Makefile` to configure paths and compiler options if needed. The defaults should work for most systems, including the warning-suppression flags this fork relies on.

3. Configure the system:

   ```bash
   make configure
   ```

   This generates configuration files in `../config/`.

4. Build the system:

   ```bash
   make world
   ```

   This builds all components and runs a quick test.

5. (Optional) Bootstrap to ensure correctness:

   ```bash
   make bootstrap
   ```

6. Install (requires superuser privileges):

   ```bash
   sudo make install
   ```

   This installs executables to `/usr/local/bin` and libraries to `/usr/local/lib/caml-light` by default.

### Building Contributed Libraries

After installing the core system, you can build additional libraries:

1. Change to `sources/contrib/`:

   ```bash
   cd ../contrib
   ```

2. Edit `Makefile` to select packages and configure paths.

3. Build:

   ```bash
   make all
   ```

4. Install:

   ```bash
   sudo make install
   ```

## Usage

### Interactive Mode

Run the interactive toplevel:

```bash
camllight
```

This starts a REPL where you can type ML expressions.

### Batch Compilation

Compile a Caml Light source file to bytecode:

```bash
camlc source.ml
```

Run the bytecode:

```bash
camlrun source.zo
```

Create a standalone executable:

```bash
camlc -custom source.ml -o executable
```

### Tools

- `camllex`: Lexer generator
- `camlyacc`: Parser generator
- `camlmktop`: Create custom toplevels

## Examples

The `sources/examples/` directory contains various example programs. To build and run an example:

```bash
cd sources/examples/basics
make
./fib
```

Or run interactively:

```bash
cd sources/examples/basics
camllight
# include "loadall";;
```

## License

Caml Light is distributed under the same license as the Objective Caml (OCaml) compiler. The full text is in [`sources/LICENSE`](sources/LICENSE), and the copyright notice is in [`sources/COPYRIGHT`](sources/COPYRIGHT). This fork adds no new license terms; the files listed under [What is new in this fork](#what-is-new-in-this-fork-not-inherited-from-upstream) are contributed under the same license.

## Contributing

This fork exists to keep Caml Light building on current systems. Issues and patches about building, bootstrapping, or CI on modern platforms are welcome here. Changes to the Caml Light language, compiler, or libraries themselves belong upstream at [`camllight/camllight`](https://github.com/camllight/camllight).
