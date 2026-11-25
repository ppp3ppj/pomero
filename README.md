# larl

A programming language with its compiler written in OCaml. The source layout is
inspired by **rustboot**, the original OCaml bootstrap compiler for Rust
([rust-lang/rust@ef75860](https://github.com/rust-lang/rust/tree/ef75860a0a72f79f97216f8aaa5b388d98da6480)).

See [doc/ROADMAP.md](doc/ROADMAP.md) for the learning plan and progress checklist.

## Directory layout

```
.
├── dune-project          dune project + opam package metadata
├── larl.opam             generated from dune-project (do not edit by hand)
├── doc/
│   └── ROADMAP.md        learning plan and todo checklist
└── src/
    ├── boot/             the compiler, written in OCaml
    │   ├── driver/       CLI entry point; wires the stages together
    │   ├── util/         shared helpers and error reporting
    │   ├── fe/           frontend: lexer, parser, AST
    │   ├── me/           middle end: name resolution, type checking
    │   └── be/           backend: code generation
    ├── lib/              standard library, written in larl
    ├── rt/               runtime support linked into compiled programs
    └── test/             test programs written in larl
        ├── run-pass/     must compile and run successfully
        ├── run-fail/     must compile but fail at runtime
        └── compile-fail/ must be rejected by the compiler
```

| Directory | dune name | Purpose |
|---|---|---|
| `src/boot/driver` | `larl` (executable) | Parses command-line arguments and runs fe → me → be. |
| `src/boot/util` | `larl_util` | Code every stage needs: source positions, diagnostics, small utilities. |
| `src/boot/fe` | `larl_fe` | Turns source text into tokens (lexer) and then into an AST (parser). |
| `src/boot/me` | `larl_me` | Checks the AST: resolves names, infers and checks types. |
| `src/boot/be` | `larl_be` | Lowers the checked program to the target (assembly, C, bytecode, …). |
| `src/lib` | — | The larl standard library (`*.larl`). Not built by dune. |
| `src/rt` | — | Runtime code (e.g. C) for memory, I/O, startup. |
| `src/test/*` | — | Language-level tests, grouped by expected outcome as in rustboot. |

## Stage dependencies

Each compiler stage is its own dune library, so dune rejects a dependency in
the wrong direction (for example, `fe` cannot use `be`):

```
util  <-  fe  <-  me  <-  be  <-  driver
```

## Build and run

```sh
dune build          # build everything
dune exec larl      # run the compiler
dune test           # run tests
```
