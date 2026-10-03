# Mini Java

**A Java-like object-oriented language implemented in OCaml.**

[![Language: OCaml](https://img.shields.io/badge/implementation-OCaml-EC6813)](Java/lib/interpreter.ml)
[![Build: Dune](https://img.shields.io/badge/build-Dune-555555)](Java/dune-project)
[![License: LGPL v3](https://img.shields.io/badge/license-LGPL_v3-blue)](Java/COPYING.LESSER)

Mini Java explores how an object-oriented language works under the hood: parsing source into an AST, loading classes, resolving methods, maintaining runtime state, and evaluating programs. It includes an interactive REPL, a pretty-printer, and an AST-based identifier-renaming demonstration.

Developed as a functional programming course project, it implements a focused subset of Java syntax and semantics. It runs source through an OCaml interpreter; Java bytecode and JVM execution are outside its scope.

[Quick start](#quick-start) · [Try the REPL](#try-the-repl) · [Language features](#language-features) · [Implementation](#implementation) · [Tests and demos](#tests-and-demos) · [Limitations](#limitations)

## Quick start

### Requirements

- OCaml and an initialized [opam](https://opam.ocaml.org/doc/Install.html) switch.
- Dune with support for the project's `2.7` configuration.
- Dependencies declared in [`Java/Java.opam`](Java/Java.opam), including Opal and the PPX packages used for tests and derived printers.

### Build and run

```bash
git clone https://github.com/bozhnyukAlex/mini-java.git
cd mini-java/Java

eval "$(opam env)"
opam install . --deps-only --with-test

dune build
dune exec ./REPL.exe
```

Run these commands from the `Java/` directory. The Makefile also provides `make all`, `make repl`, and `make test` shortcuts once dependencies are installed.

## Try the REPL

The REPL accepts **method definitions, statements, and expressions**. Finish each input with `@`; this delimiter tells the REPL to evaluate the accumulated input.

### Recursive factorial

Define two methods, then evaluate an expression:

```text
> int fac1(int acc, int n) { if (n <= 1) return acc; else return fac1(acc * n, n - 1); }@
Method added
> int fac(int n) { return fac1(1, n); }@
Method added
> fac(5)@
Result: VInt (120)
```

### Array type checks

The REPL preloads sample classes such as `Figure`, `Circle`, and `QuickSorter`. These allow you to explore object arrays and assignment checks immediately:

```text
> Object[] x = new Object[3];@
Statement evaluated
> x[0] = new Circle(5);@
ArrayStoreException
> Object[] y = new Figure[3];@
Statement evaluated
> y[0] = new QuickSorter();@
Wrong assign type!
```

These examples demonstrate the interpreter's array semantics, which differ from standard Java. See [Limitations](#limitations).

### REPL commands

| Command | Purpose |
| --- | --- |
| `show_available_methods@` | List methods defined in the current REPL session |
| `show_var_table@` | Inspect local variables and their runtime values |
| `show_curr_stdlib@` | Display the preloaded sample classes |
| `exit@` | Exit the REPL |

## Language features

| Area | Implemented functionality |
| --- | --- |
| Values and expressions | Integers, booleans, characters, strings, arithmetic, comparisons, and logical operators |
| Control flow | `if` / `else`, `while`, `for`, `break`, `continue`, and `return` |
| Objects | Classes, fields, methods, object creation, and mutable object state |
| Inheritance | Single inheritance, abstract classes and methods, method overriding, `this`, and `super` |
| Methods and constructors | Method overloading, recursive calls, constructors, and constructor chaining |
| Arrays | One-dimensional arrays, initialization, indexing, element updates, and assignment checks |
| Modifiers | Support for modifiers including `public`, `static`, `final`, and `abstract` within the implemented subset |
| Built-in classes | `Object` and a small `String` implementation with operations such as `length`, `concat`, and `startsWith` |
| Language tooling | REPL, pretty-printing, and an AST-based identifier-renaming demonstration |

The demos exercise these features with recursive factorial, sorting, constructor chains, inheritance, and the Visitor pattern.

## Implementation

The parser builds an AST using Opal parser combinators. The class loader prepares class definitions, inheritance relationships, methods, and constructors. The interpreter evaluates expressions and statements against runtime contexts containing object references, variables, scope information, and control-flow signals.

| Source | Responsibility |
| --- | --- |
| [`Java/lib/ast.ml`](Java/lib/ast.ml) | AST definitions, runtime values, object and array references |
| [`Java/lib/parser.ml`](Java/lib/parser.ml) | Parsers for expressions, statements, methods, and classes |
| [`Java/lib/interpreter.ml`](Java/lib/interpreter.ml) | Class loading, built-in classes, runtime checks, and evaluation |
| [`Java/lib/pretty_printer.ml`](Java/lib/pretty_printer.ml) | Formatting AST nodes as Java-like source |
| [`Java/lib/transform.ml`](Java/lib/transform.ml) | Identifier-renaming demonstration with formatted change output |
| [`Java/REPL.ml`](Java/REPL.ml) | Interactive session, sample classes, and inspection commands |
| [`Java/lib/tests.ml`](Java/lib/tests.ml) | Inline tests |
| [`Java/demos/`](Java/demos/) | Executable examples and expected output |

## Tests and demos

From `Java/`, run the existing test suite:

```bash
dune runtest
```

The project includes inline tests and Cram tests that compare demo output against [`Java/demos/tests.t`](Java/demos/tests.t).

Run individual demos to inspect each stage:

```bash
dune exec ./demos/demoParserFirst.exe
dune exec ./demos/demoClassLoader.exe
dune exec ./demos/demoInterpreter.exe
dune exec ./demos/demoPrettyPrinter.exe
dune exec ./demos/demoTransformation.exe
```

`demoInterpreter` covers arithmetic, scope, loops, mutable objects and arrays, inheritance, recursion, constructor chaining, final fields and variables, overloading, and error cases. `demoTransformation` demonstrates renaming identifiers and prints the corresponding source changes.

## Limitations

This is an educational language implementation with deliberately limited Java compatibility:

- Class names cannot be reserved keywords.
- Whole-program execution expects a single `main` entry point with no arguments.
- Multidimensional arrays and explicit casts such as `(Type) value` are unsupported.
- Prefix and postfix increment are treated as incrementing assignments; their semantics do not fully match Java.
- Stack overflow is not detected by the language runtime.
- Array element checks can reject assignments that standard Java would allow, as shown in the REPL example.
- Overridden `equals` methods are used for object equality; otherwise references are compared. This differs from standard Java's distinction between `==` and `equals`.

## Author and license

Created by [Alexander Bozhnyuk](https://github.com/bozhnyukAlex).

Licensed under **GNU LGPL v3**. See [`Java/COPYING.LESSER`](Java/COPYING.LESSER) and [`Java/COPYING`](Java/COPYING).
