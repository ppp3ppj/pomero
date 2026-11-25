# larl roadmap

Learning plan for building larl: an OCaml front end with a QBE back end.
Tick a box (`- [x]`) when it's done. Finish each phase's **Done when** check
before you move on, so the compiler always works end to end.

```
hello.larl → [larl, OCaml] → hello.ssa → qbe → hello.s → cc → ./hello
```

---

## Phase 0: OCaml basics

- [x] Install tools: `opam`, `dune`, `utop`, `ocaml-lsp-server`, `ocamlformat`
- [ ] Read *Real World OCaml*, chapters 1–7
- [ ] Understand `let`, functions, and currying
- [ ] Understand variants (`type t = A | B of int`)
- [ ] Understand pattern matching (`match ... with`)
- [ ] Understand recursion and `List.map`, `List.fold_left`
- [ ] Understand `option` and `result`
- [ ] Understand records
- [ ] Understand modules and `.mli` files
- [ ] Exercise: write `eval : expr -> int` for `Int | Add | Sub | Mul | Div`

**Done when:** you can write the calculator exercise without looking anything up.

## Phase 1: AST (`src/boot/fe/ast.ml`)

- [ ] Integer literals
- [ ] Binary operators (`+ - * /`)
- [ ] `let` bindings and variables
- [ ] `print` statement
- [ ] A `program` type (a list of statements)
- [ ] Pretty-printer: `Ast.to_string`

**Done when:** an AST built by hand in OCaml prints back as larl source.

## Phase 2: Lexer (`src/boot/fe/lexer.mll`)

- [ ] Read the RWO chapter "Parsing with OCamllex and Menhir"
- [ ] Add `(ocamllex lexer)` to `src/boot/fe/dune`
- [ ] Tokens: integers, identifiers, keywords, operators, parentheses
- [ ] Skip whitespace and comments
- [ ] Track line numbers for error messages
- [ ] Report unknown characters as errors

**Done when:** `larl --tokens file.larl` prints the token list.

## Phase 3: Parser (`src/boot/fe/parser.mly`)

- [ ] `opam install menhir`
- [ ] Add `(menhir (modules parser))` to `src/boot/fe/dune`
- [ ] Grammar for expressions and statements
- [ ] Operator precedence (`*` binds tighter than `+`)
- [ ] Syntax errors include the line number

**Done when:** `larl --ast file.larl` prints the AST.

## Phase 4: Interpreter (optional, `src/boot/me/eval.ml`)

- [ ] `eval` with an environment for variables
- [ ] First tests in `src/test/run-pass/*.larl`

**Done when:** `larl --run file.larl` prints the right output.

## Phase 5: QBE by hand

- [ ] Install QBE (`pacman -S qbe`)
- [ ] Read the QBE IL docs: <https://c9x.me/compile/doc/il.html>
- [ ] Hand-write `hello.ssa` that calls `printf`
- [ ] Hand-write arithmetic with temporaries
- [ ] Hand-write a function and a call
- [ ] Hand-write an `if` and a loop (`jnz`, labels)
- [ ] Build with `qbe x.ssa > x.s && cc x.s -o x`

**Done when:** all hand-written `.ssa` files compile and run.

## Phase 6: Code generation (`src/boot/be/codegen.ml`)

- [ ] Emit `export function w $main()`
- [ ] Generate fresh temporaries `%t1`, `%t2`, …
- [ ] Integer literals and binary operators
- [ ] `let` and variables
- [ ] `print` through `call $printf`
- [ ] Emit the format string as a `data` definition

**Done when:** the generated `.ssa` runs and gives the same output as the interpreter.

## Phase 7: Driver (`src/boot/driver/main.ml`)

- [ ] Read the source file
- [ ] Run lex → parse → codegen
- [ ] Write the `.ssa` file
- [ ] Run `qbe` and `cc` with `Sys.command`
- [ ] `-o <output>` flag
- [ ] Clear error messages and non-zero exit codes

**Done when:** `larl hello.larl -o hello && ./hello` works.

## Phase 8: Test runner

- [ ] `run-pass`: compiles, exits 0, and output matches `<name>.out`
- [ ] `run-fail`: compiles, then exits non-zero
- [ ] `compile-fail`: the compiler rejects it
- [ ] Hook the runner into `dune test` (or a `Makefile` target)

**Done when:** one command runs every test and reports pass/fail.

## Phase 9: Grow the language

Add one feature and one test at a time.

- [ ] `if` / `else`
- [ ] `while` loops
- [ ] Functions and calls
- [ ] `bool` type and comparisons
- [ ] Type checker (`src/boot/me/typecheck.ml`)
- [ ] Strings
- [ ] Structs
- [ ] Arrays

---

## Resources

- *Real World OCaml*: <https://dev.realworldocaml.org>
- *Crafting Interpreters*: <https://craftinginterpreters.com>
- QBE IL reference: <https://c9x.me/compile/doc/il.html>
- cproc, a C compiler that uses QBE: <https://sr.ht/~mcf/cproc>
- Hare, a language that uses QBE: <https://harelang.org>
