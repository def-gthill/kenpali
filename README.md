# Kenpali

Kenpali is a minimalistic programming language, designed for scripting other applications. The minimalism is meant to make writing new implementations easier.

Kenpali is a *pure functional* programming language. Kenpali programs cannot *do* anything, they can only compute values. This allows applications to execute arbitrary user-submitted Kenpali code without exposing the system to attack.

Kenpali can be written in two main forms. *Kenpali Code* is designed for human programmers, with a syntax loosely based on JavaScript. Meanwhile, *Kenpali JSON* is a direct encoding of the abstract syntax tree (AST) in JSON, allowing for easy manipulation of Kenpali programs by other code (including the program itself!). At minimum, a Kenpali implementation must provide a `kpparse` function to convert Kenpali Code to Kenpali JSON, and a `kpeval` function to evaluate a Kenpali JSON expression and return the result.

## Crash Course

### Data Types

All [JSON values](https://www.json.org/json-en.html) are also valid Kenpali values:

- `null`
- booleans: `true` and `false`
- numbers: `42`, `-1.5`, `3e-4`, etc.
- strings: `"foo"`, `"\u1234\n\\\""`, etc.
- arrays: `[1, "two", [null, true, false]]`, etc.
- objects: `{"answer": 42, "question": null}`, etc.

### Expressions

Kenpali works by evaluating expressions. You can experiment with Kenpali expressions in the REPL.

The simplest expression is just a single JSON value, which evaluates
to itself:

```
kenpali> 42
42
kenpali> ["foo", "bar"]
["foo", "bar"]
kenpali> null
null
```

To build more complex Kenpali expressions, you call functions. For example, the `negative` function flips the sign of a number:

```
kenpali> negative(42)
-42
kenpali> negative(-1.5)
1.5
```

Some functions, like `plus` and `times`, take multiple arguments:

```
kenpali> plus(1, 2)
3
kenpali> times(2, 3)
6
```

Kenpali doesn't have special operators like `+` or `*` for the basic arithmetic and logic operations, using named functions instead. Minimalism takes sacrifice!

Naturally, you can nest function calls to send the output of one function into another function:

```
kenpali> times(plus(1, 2), plus(3, 4))
21
```

### Pipelines

Nesting lots of function calls can make programs hard to read.

```
kenpali> dividedBy(minus(times(plus(2, 3), 4), 5), 6)
2.5
```

Kenpali provides a *forward-pipe* operator `|` to help programs flow more naturally. The result of the expression on the left of the operator is passed to the function on the right side as its first argument. So `x | f` is the same as `f(x)`, while `x | f(y)` is the same as `f(x, y)`. This means we can rewrite the above example as:

```
kenpali> 2 | plus(3) | times(4) | minus(5) | dividedBy(6)
2.5
```

Using the forward-pipe operator lets you express a program as a sequence of computational steps. This makes it easier to substitute one of the steps or combine them in a different way.
