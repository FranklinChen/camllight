# Caml Light

[![CI](https://github.com/FranklinChen/camllight/actions/workflows/ci.yml/badge.svg)](https://github.com/FranklinChen/camllight/actions/workflows/ci.yml)

Caml Light is a small, portable implementation of the ML language, designed to run on most Unix machines. It has also been ported to the Macintosh, the PC, and other microcomputers.

Caml Light implements the Caml language, a functional language from the ML family. Caml is quite close to Standard ML, though not strictly conformant. There are some slight differences in syntax and semantics, and major differences in the module system.

This is a revival of the Caml Light system, originally developed by INRIA, with updates to make it compile on modern systems.

## Features

- Bytecode compiler and interpreter
- Interactive toplevel system
- Batch compiler for standalone programs
- Lexer and parser generators (camllex and camlyacc)
- Standard library
- Various contributed libraries and tools

## Building

### Prerequisites

- A C compiler (gcc recommended)
- Make
- Unix-like environment

### Build Steps

1. Change to the `sources/src/` directory:

   ```bash
   cd sources/src
   ```

2. Edit the `Makefile` to configure paths and compiler options if needed. The defaults should work for most systems.

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

## Documentation

- Reference manual and tutorial are available in the `doc/` directory in various formats.
- Original documentation can be found at the INRIA Caml site (archived).

## License

Caml Light is distributed under the same license as the Objective Caml compiler. See `sources/LICENSE` for details.

## Contributing

This is a historical project revival. Bug reports and patches are welcome via GitHub issues and pull requests.

## History

Caml Light was developed at INRIA in the 1990s. This repository includes updates to make it compile on modern systems.
