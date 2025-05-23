# Kenpali

THIS LANGUAGE IS EXPERIMENTAL AND ANY PART OF IT MAY CHANGE AT ANY TIME. DO NOT USE IT FOR ANYTHING THAT MATTERS.

Kenpali is a minimalistic programming language, designed for scripting other applications. The minimalism is meant to make writing new implementations easier—and to make it easier to secure the system against the risks of executing arbitrary user-submitted code.

Kenpali is an *expression-oriented* language—every Kenpali program is an expression that can be nested inside other expressions. This makes it easier to build up programs incrementally and rearrange their parts into new combinations.

Kenpali can be written in two main forms. *Kenpali Code* is designed for human programmers, with a syntax loosely based on JavaScript. Meanwhile, *Kenpali JSON* is a direct encoding of the abstract syntax tree (AST) in JSON, allowing for easy manipulation of Kenpali programs by other code (including the program itself!). At minimum, a Kenpali implementation must provide a `kpparse` function to convert Kenpali Code to Kenpali JSON, and a `kpeval` function to evaluate a Kenpali JSON expression and return the result.

This repo contains specifications for both Kenpali Code and Kenpali JSON.

For a working implementation of this specification, see the [Kenpali JS](https://github.com/def-gthill/kenpali-js) repo.

To give Kenpali a try, see [the demo site](https://www.kenpali.org/)!
