# Kenpali

Kenpali is a minimalistic programming language, designed for scripting other applications. The minimalism is meant to make writing new implementations easier.

Kenpali is a *pure functional* programming language. Kenpali programs cannot *do* anything, they can only compute values. This allows applications to execute arbitrary user-submitted Kenpali code without exposing the system to attack.

Kenpali can be written in two main forms. *Kenpali Code* is designed for human programmers, with a syntax loosely based on JavaScript. Meanwhile, *Kenpali JSON* is a direct encoding of the abstract syntax tree (AST) in JSON, allowing for easy manipulation of Kenpali programs by other code (including the program itself!). At minimum, a Kenpali implementation must provide a `kpparse` function to convert Kenpali Code to Kenpali JSON, and a `kpeval` function to evaluate a Kenpali JSON expression and return the result.

<!-- ## Syntax Overview -->

<!-- Kenpali Code supports the following features:

- Literal types based on JSON: `null`; booleans `true` and `false`; numbers like `0`, `42`, and `-1.5`; and strings like `"foo"`.
- Arrays containing arbitrary values, e.g. `[4, 5, "six"]`.
- Objects containing string keys and arbitrary values, e.g. `{"foo": 42, "bar": "baz"}`.
- Function calls: `inc(1)` calls the function `inc` (increment) with `1` as its argument, yielding `2`.
- Scopes with declared names: `x = 1; inc(x)` also calls the function `inc` with `1` as its value, since `x` is defined to have the value `1`.
- Function definitions: `foo = (x) => plus(x, 3); foo(5)` yields 8, because `foo` is defined as a function that adds 3 to its argument.
- Optional parameters: `foo = (x, y: 3) => plus(x, 3); [foo(5), foo(5, 4)]` yields `[8, 9]`, because the second parameter is assigned the value `3` if no second argument is provided.
- Keyword parameters: defining the function as `foo = (x=) => plus(x, 3)` means it has to be called as `foo(x = 5)`, not just `foo(5)`.
- Optional *arguments*:  -->
