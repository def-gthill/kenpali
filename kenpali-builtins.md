# Kenpali Builtins Specification

Builtins are functions that must be provided by the host platform.

## Arithmetic

```
# Plus
[plus(), plus(1), plus(1, 2), plus(1, 2, 3)]
>> [0, 1, 3, 6]
```

```
# Negative
[negative(42), negative(-1.5)]
>> [-42, 1.5]
```

```
# Times
[times(), times(2), times(2, 3), times(2, 3, 4)]
>> [1, 2, 6, 24]
```

```
# One over
[oneOver(2), oneOver(-0.5)]
>> [0.5, -2]
```

```
# Divide with remainder
[
    divideWithRemainder(10, 3),
    divideWithRemainder(-10, 3),
    divideWithRemainder(10, -3),
    divideWithRemainder(-10, -3),
]
>> [
    {quotient: 3, remainder: 1},
    {quotient: -4, remainder: 2},
    {quotient: -4, remainder: -2},
    {quotient: 3, remainder: -1}
]
```

## Strings

```
# String length
[length(""), length("f"), length("foo")]
>> [0, 1, 3]
```

```
# Join
[join(), join("foo"), join("foo", "bar", "baz")]
>> ["", "foo", "foobarbaz"]
```

## Comparison

```
# Equals
[
    [equals(null, null)],
    [equals(true, true), equals(false, true), equals(false, false)],
    [equals(42, 42), equals(42, 43)],
    [equals("foo", "foo"), equals("foo", "bar")],
    [
        equals(["foo", "bar"], ["foo", "bar"]),
        equals(["foo", "bar"], ["foo", "baz"]),
    ],
    [
        equals([[1, 2], [3, 4]], [[1, 2], [3, 4]]),
        equals([[1, 2], [3, 4]], [[1, 2], [3, 5]]),
    ],
    [
        equals(
            {foo: "bar", spam: "eggs"},
            {spam: "eggs", foo: "bar"},
        ),
        equals(
            {foo: "bar", spam: "eggs"},
            {spam: "eggs", foo: "baz"},
        ),
    ],
]
>> [
    [true],
    [true, false, true],
    [true, false],
    [true, false],
    [true, false],
    [true, false],
    [true, false]
]
```

```
# Less than
[
    [
        false | isLessThan(true),
        true | isLessThan(true),
        true | isLessThan(false),
    ],
    [
        42 | isLessThan(43),
        43 | isLessThan(43),
        43 | isLessThan(42),
    ],
    [
        "bar" | isLessThan("baz"),
        "baz" | isLessThan("baz"),
        "baz" | isLessThan("bar"),
    ],
    [
        ["foo", "bar"] | isLessThan(["foo", "baz"]),
        ["foo", "bar"] | isLessThan(["foo!", "aar"]),
        ["foo", "bar"] | isLessThan(["foo", "bar"]),
        ["foo", "baz"] | isLessThan(["foo", "bar"]),
        ["foo!", "aar"] | isLessThan(["foo", "bar"]),
    ],
    [
        [[1, 2], [3, 4]] | isLessThan([[1, 2], [3, 10]]),
        [[1, 2], [3, 4]] | isLessThan([[1, 3], [3, 3]]),
        [[1, 2], [3, 4]] | isLessThan([[1, 2], [3, 4]]),
        [[1, 2], [3, 10]] | isLessThan([[1, 2], [3, 4]]),
        [[1, 3], [3, 3]] | isLessThan([[1, 2], [3, 4]]),
    ]
]
>> [
    [true, false, false],
    [true, false, false],
    [true, false, false],
    [true, true, false, false, false],
    [true, true, false, false, false]
]
```

## Logic

```
# And
[
    and(true, true),
    and(true, false),
    and(false, true),
    and(false, false),
]
>> [true, false, false, false]
```

```
# Or
[
    or(true, true),
    or(true, false),
    or(false, true),
    or(false, false),
]
>> [true, true, true, false]
```

```
# Not
[
    not(true),
    not(false),
]
>> [false, true]
```

## Types and Type Conversion

```
# Type of
[
    typeOf(null),
    typeOf(false),
    typeOf(true),
    typeOf(42),
    typeOf("foo"),
    typeOf([1, 2, 3]),
    typeOf({foo: 1, bar: 2}),
    typeOf(typeOf),
    typeOf((x) => x),
    typeOf((1 @ 1)!),
]
>> ["null", "boolean", "boolean", "number", "string", "array", "record", "builtin", "given", "error"]
```

The `typeOf` function never returns `"object"` or `"function"`, since these terms aren't specific enough. The term "object" includes records, givens, and errors (i.e. anything that can be serialized to a JSON object), while the term "function" includes builtins and givens (i.e. anything that can be called).

```
# To string
[
    toString(null),
    toString(false),
    toString(true),
    toString(42),
    toString(-2.5),
    toString("foo"),
    toString([1, 2, 3]),
    toString({foo: "bar", "spam!": "eggs"}),
    toString(toString),
]
>> [
    "null",
    "false",
    "true",
    "42",
    "-2.5",
    "\"foo\"",
    "[1, 2, 3]",
    "{foo: \"bar\", \"spam!\": \"eggs\"}",
    "function toString",
]
```

```
# To number
[
    toNumber("1"),
    toNumber("-2.5"),
    toNumber(42),
]
>> [1, -2.5, 42]
```

## Control Flow

```
# If
[
    if(true, then: 1, else: 2),
    if(false, then: 1, else: 2),
]
>> [1, 2]
```

The `repeat` function repeatedly updates a value by applying the specified function. When the `while` property becomes `true`, the `next` property is ignored, and the final result is the *previous* value.

```
# Repeat
repeat(
    1,
    (previous) => {
        while: previous | isLessThan(1000),
        next: previous | times(2),
    }
)
>> 1024
```

## Arrays

```
# Indexing arrays
array = ["foo", "bar"]
[
    array | at(2),
    array | at(1),
]
>> ["bar", "foo"]
```

```
# Array length
[
    length([]),
    length(["foo"]),
    length(["foo", "bar", "baz"]),
]
>> [0, 1, 3]
```

The `build` function generates an array by repeatedly applying a function to a start value. When the `while` property becomes `true`, the `out` and `next` properties are ignored; the final array will contain all *previous* values of `out`.

```
# Build
build(
    [1, 1],
    (previous) => {
        while: previous @ 1 | isLessThan(20),
        out: previous @ 1,
        next: [previous @ 2, plus(previous @ 1, previous @ 2)],
    }
)
>> [1, 1, 2, 3, 5, 8, 13]
```

## Objects

```
# Indexing objects
object = {foo: "bar", spam: "eggs"};
[
    object | at("spam"),
    object | at("foo"),
]
>> ["eggs", "bar"]
```
