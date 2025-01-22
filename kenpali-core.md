# Kenpali Core Specification

The Core functions must be available to any Kenpali program. Reference implementations for some of them in terms of the others are provided by `core.kpc`, enabling a complete implementation with fewer builtins.

## Arithmetic

```
# Plus
[plus(), plus(1), plus(1, 2), plus(1, 2, 3)]
>> [0, 1, 3, 6]
```

```
# Sum
sum(1 | to(10))
>> 55
```

```
# Minus
[5 | minus(2), 2 | minus(5)]
>> [3, -3]
```

```
# Negative
[negative(42), negative(-1.5)]
>> [-42, 1.5]
```

```
# Absolute value
[
    42 | absolute,
    -42 | absolute,
]
>> [42, 42]
```

```
# Increment
[3 | up, -3 | up, 1.5 | up]
>> [4, -2, 2.5]
```

```
# Decrement
[3 | down, -3 | down, 1.5 | down]
>> [2, -4, 0.5]
```

```
# Times
[times(), times(2), times(2, 3), times(2, 3, 4)]
>> [1, 2, 6, 24]
```

```
# Divided by
[12 | dividedBy(3), 12 | dividedBy(8)]
>> [4, 1.5]
```

```
# One over
[oneOver(2), oneOver(-0.5)]
>> [0.5, -2]
```

```
# Integer quotient
[
    10 | quotientBy(3),
    -10 | quotientBy(3),
    10 | quotientBy(-3),
    -10 | quotientBy(-3),
    10.5 | quotientBy(3),
    10 | quotientBy(3.5),
]
>> [3, -4, -4, 3, 3, 2]
```

```
# Remainder
[
    10 | remainderBy(3),
    -10 | remainderBy(3),
    10 | remainderBy(-3),
    -10 | remainderBy(-3),
    10.5 | remainderBy(3),
    10 | remainderBy(3.5),
]
>> [1, 2, -2, -1, 1.5, 3]
```

```
# Divisible by
[
    10 | isDivisibleBy(5),
    10 | isDivisibleBy(3),
    9 | isDivisibleBy(3),
]
>> [true, false, true]
```


## Strings

```
# Converting a string to code points
toCodePoints("foo\u1234")
>> [102, 111, 111, 4660]
```

```
# Converting code points to a string
fromCodePoints([102, 111, 111, 4660])
>> "foo\u1234"
```

```
# String length
[length(""), length("f"), length("foo")]
>> [0, 1, 3]
```

```
# Joining strings
[
    join([]),
    join(["foo"]),
    join(["foo", "bar", "baz"]),
    join(["foo", "bar", "baz"], on: "|"),
]
>> ["", "foo", "foobarbaz", "foo|bar|baz"]
```

```
# Joining lines
["foo", "bar", "", "baz"] | joinLines
>> "foo\nbar\n\nbaz"
```

```
# Splitting on a delimiter
[
    "foo,bar,,baz" | split(on: ","),
    "foo=>bar=>=>baz" | split(on: "=>"),
    "foo||bar||||baz" | split(on: "||"),
    "|foo||bar|" | split(on: "|"),
]
>> [
    ["foo", "bar", "", "baz"],
    ["foo", "bar", "", "baz"],
    ["foo", "bar", "", "baz"],
    ["", "foo", "", "bar", ""],
]
```

```
# Splitting lines
"foo\nbar\n\nbaz" | splitLines
>> ["foo", "bar", "", "baz"]
```

```
# Slicing strings
[
    "foobar" | slice(from: 2, to: 4),
    "foobar" | slice(from: 2, to: 10),
    "foobar" | slice(from: 0, to: 4),
]
>> [
    "oob",
    "oobar",
    "foob",
]
```

```
# Dropping leading and trailing characters
[
    "foobar" | dropFirst,
    "foobar" | dropFirst(2),
    "foobar" | dropLast,
    "foobar" | dropLast(2),
]
>> [
    "oobar",
    "obar",
    "fooba",
    "foob",
]
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

```
# At most (less than or equal)
[
    42 | isAtMost(43),
    43 | isAtMost(43),
    43 | isAtMost(42),
]
>> [true, true, false]
```

```
# More than (greater than)
[
    42 | isMoreThan(43),
    43 | isMoreThan(43),
    43 | isMoreThan(42),
]
>> [false, false, true]
```

```
# At least (greater than or equal)
[
    42 | isAtLeast(43),
    43 | isAtLeast(43),
    43 | isAtLeast(42),
]
>> [false, true, true]
```

```
# Between
foo = (n) => n | isBetween(42, 97);
[
    foo(1),
    foo(41),
    foo(42),
    foo(64),
    foo(97),
    foo(98),
    foo(216),
]
>> [
    false,
    false,
    true,
    true,
    true,
    false,
    false,
]
```

```
# Least
[97, 42, 216] | least
>> 42
```

```
# Most
[97, 42, 216] | most
>> 216
```

## Logic

```
# And
[
    and(true, () => true),
    and(true, () => false),
    and(false, () => true),
    and(false, () => false),
]
>> [true, false, false, false]
```

```
# And short-circuiting
[
    and(false, () => [] @ 1),
    and(false, () => (foo = foo; foo)),
]
>> [false, false]
```

```
# Or
[
    or(true, () => true),
    or(true, () => false),
    or(false, () => true),
    or(false, () => false),
]
>> [true, true, true, false]
```

```
# Or short-circuiting
[
    or(true, () => [] @ 1),
    or(true, () => (foo = foo; foo)),
]
>> [true, true]
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
    typeOf(1 | to(3)),
    typeOf({foo: 1, bar: 2}),
    typeOf(typeOf),
    typeOf((x) => x),
    typeOf((1 @ 1)!),
]
>> ["null", "boolean", "boolean", "number", "string", "array", "stream", "object", "builtin", "given", "error"]
```

The `typeOf` function never returns `"function"`, since there are two distinct types of functions: _givens_ (functions defined within Kenpali) and _builtins_ (functions defined in the host language).

```
# Is null
[
    isNull(null),
    isNull(false),
    isNull(true),
    isNull(42),
    isNull("foo"),
    isNull([1, 2, 3]),
    isNull(1 | to(3)),
    isNull({foo: 1, bar: 2}),
    isNull(typeOf),
    isNull((x) => x),
    isNull((1 @ 1)!),
]
>> [true, false, false, false, false, false, false, false, false, false, false]
```

```
# Is boolean
[
    isBoolean(null),
    isBoolean(false),
    isBoolean(true),
    isBoolean(42),
    isBoolean("foo"),
    isBoolean([1, 2, 3]),
    isBoolean(1 | to(3)),
    isBoolean({foo: 1, bar: 2}),
    isBoolean(typeOf),
    isBoolean((x) => x),
    isBoolean((1 @ 1)!),
]
>> [false, true, true, false, false, false, false, false, false, false, false]
```

```
# Is number
[
    isNumber(null),
    isNumber(false),
    isNumber(true),
    isNumber(42),
    isNumber("foo"),
    isNumber([1, 2, 3]),
    isNumber(1 | to(3)),
    isNumber({foo: 1, bar: 2}),
    isNumber(typeOf),
    isNumber((x) => x),
    isNumber((1 @ 1)!),
]
>> [false, false, false, true, false, false, false, false, false, false, false]
```

```
# To number
[
    toNumber("1"),
    toNumber("-2.5"),
    toNumber("05"),
    toNumber(42),
]
>> [1, -2.5, 5, 42]
```

```
# Is string
[
    isString(null),
    isString(false),
    isString(true),
    isString(42),
    isString("foo"),
    isString([1, 2, 3]),
    isString(1 | to(3)),
    isString({foo: 1, bar: 2}),
    isString(typeOf),
    isString((x) => x),
    isString((1 @ 1)!),
]
>> [false, false, false, false, true, false, false, false, false, false, false]
```

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
    toString(1 | to(3)),
    toString({foo: "bar", "spam!": "eggs"}),
    toString(toString),
    toString((1 @ 1)!),
]
>> [
    "null",
    "false",
    "true",
    "42",
    "-2.5",
    "\"foo\"",
    "[1, 2, 3]",
    "stream [...]",
    "{foo: \"bar\", \"spam!\": \"eggs\"}",
    "function toString",
    "error wrongType {value: 1, expectedType: {either: [\"sequence\", \"object\"]}}"
]
```

Calling `toString` on a stream reports already evaluated elements but never forces further evaluation.

```
# To string on streams
stream = 1 | to(4);
[
    stream | toString,
    (
        stream | keepFirst(3) | toArray;
        stream | toString
    ),
    (
        stream | toArray;
        stream | toString
    ),
]
>> [
    "stream [...]",
    "stream [1, 2, 3...]",
    "stream [1, 2, 3, 4]",
]
```

```
# To string on givens
foo = () => (
    bar = () => 42;
    () => bar
);
[
    (() => 42) | toString,
    foo | toString,
    foo() | toString,
    foo()() | toString,
]
>> [
    "function $anon1",
    "function foo",
    "function foo/$anon1",
    "function foo/bar",
]
```

```
# Is array
[
    isArray(null),
    isArray(false),
    isArray(true),
    isArray(42),
    isArray("foo"),
    isArray([1, 2, 3]),
    isArray(1 | to(3)),
    isArray({foo: 1, bar: 2}),
    isArray(typeOf),
    isArray((x) => x),
    isArray((1 @ 1)!),
]
>> [false, false, false, false, false, true, false, false, false, false, false]
```

```
# Is stream
[
    isStream(null),
    isStream(false),
    isStream(true),
    isStream(42),
    isStream("foo"),
    isStream([1, 2, 3]),
    isStream(1 | to(3)),
    isStream({foo: 1, bar: 2}),
    isStream(typeOf),
    isStream((x) => x),
    isStream((1 @ 1)!),
]
>> [false, false, false, false, false, false, true, false, false, false, false]
```

```
# Is object
[
    isObject(null),
    isObject(false),
    isObject(true),
    isObject(42),
    isObject("foo"),
    isObject([1, 2, 3]),
    isObject(1 | to(3)),
    isObject({foo: 1, bar: 2}),
    isObject(typeOf),
    isObject((x) => x),
    isObject((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, true, false, false, false]
```

```
# Is builtin
[
    isBuiltin(null),
    isBuiltin(false),
    isBuiltin(true),
    isBuiltin(42),
    isBuiltin("foo"),
    isBuiltin([1, 2, 3]),
    isBuiltin(1 | to(3)),
    isBuiltin({foo: 1, bar: 2}),
    isBuiltin(typeOf),
    isBuiltin((x) => x),
    isBuiltin((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, false, true, false, false]
```

```
# Is given
[
    isGiven(null),
    isGiven(false),
    isGiven(true),
    isGiven(42),
    isGiven("foo"),
    isGiven([1, 2, 3]),
    isGiven(1 | to(3)),
    isGiven({foo: 1, bar: 2}),
    isGiven(typeOf),
    isGiven((x) => x),
    isGiven((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, false, false, true, false]
```

```
# Is error
[
    isError(null),
    isError(false),
    isError(true),
    isError(42),
    isError("foo"),
    isError([1, 2, 3]),
    isError(1 | to(3)),
    isError({foo: 1, bar: 2}),
    isError(typeOf),
    isError((x) => x),
    isError((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, false, false, false, true]
```

```
# Is function
[
    isFunction(null),
    isFunction(false),
    isFunction(true),
    isFunction(42),
    isFunction("foo"),
    isFunction([1, 2, 3]),
    isFunction(1 | to(3)),
    isFunction({foo: 1, bar: 2}),
    isFunction(typeOf),
    isFunction((x) => x),
    isFunction((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, false, true, true, false]
```

```
# To function
[
    toFunction(42)(97),
    toFunction(toString)(97),
    toFunction((x) => plus(x, 3))(97),
]
>> [42, "97", 100]
```

```
# Is sequence
[
    isSequence(null),
    isSequence(false),
    isSequence(true),
    isSequence(42),
    isSequence("foo"),
    isSequence([1, 2, 3]),
    isSequence(1 | to(3)),
    isSequence({foo: 1, bar: 2}),
    isSequence(typeOf),
    isSequence((x) => x),
    isSequence((1 @ 1)!),
]
>> [false, false, false, false, true, true, true, false, false, false, false]
```

## Control Flow

```
# If
[
    if(true, then: () => 1, else: () => 2),
    if(false, then: () => 1, else: () => 2),
]
>> [1, 2]
```

```
# If short-circuiting
[
    if(true, then: () => 1, else: () => [] @ 1),
    if(true, then: () => 1, else: () => (foo = foo; foo)),
    if(false, then: () => [] @ 1, else: () => 2),
    if(false, then: () => (foo = foo; foo), else: () => 2),
]
>> [1, 1, 2, 2]
```

```
# But if
[
    42 | butIf((x) => (x | isMoreThan(10)), (x) => (x | minus(5))),
    7 | butIf((x) => (x | isMoreThan(10)), (x) => (x | minus(5))),
]
>> [37, 7]
```

```
# Multi-way if
foo = (a, b) => ifs(
    [() => a | isLessThan(b), () => "Too small!"],
    [() => a | isMoreThan(b), () => "Too big!"],
    else: () => "Just right!",
);
[
    foo(97, 42),
    foo(1, 42),
    foo(42, 42),
]
>> ["Too big!", "Too small!", "Just right!"]
```

```
# Switch
withComment = (value, comment) => [value | toString, comment] | join;
foo = | switch(
    [| isLessThan(42), | withComment(" is too small!")],
    [| isMoreThan(42), | withComment(" is too big!")],
    else: | withComment(" is just right!"),
);
[
    foo(97),
    foo(1),
    foo(42),
]
>> ["97 is too big!", "1 is too small!", "42 is just right!"]
```

## Stream Builders

These functions build up new streams from scalar inputs.

The `build` function generates a stream by repeatedly applying a function to a start value.

```
# Build
powersOfTwo = 1 | build(| times(2));
[
    powersOfTwo | isStream,
    powersOfTwo @ 1,
    powersOfTwo @ 3,
    powersOfTwo @ 8,
]
>> [true, 1, 4, 128]
```

```
# Ranges
[
    1 | to(5) | isStream,
    1 | to(5) | toArray,
    5 | to(10) | toArray,
]
>> [
    true,
    [1, 2, 3, 4, 5],
    [5, 6, 7, 8, 9, 10],
]
```

```
# Ranges with step
[
    1 | to(10, by: 3) | toArray,
    5 | to(10, by: 2) | toArray,
]
>> [
    [1, 4, 7, 10],
    [5, 7, 9],
]
```

```
# Ranges with negative step
[
    5 | to(1, by: -1) | toArray,
    10 | to(5, by: -2) | toArray,
]
>> [
    [5, 4, 3, 2, 1],
    [10, 8, 6],
]
```

```
# Ranges defined by size
[
    1 | toSize(5) | isStream,
    1 | toSize(5) | toArray,
    5 | toSize(6) | toArray,
]
>> [
    true,
    [1, 2, 3, 4, 5],
    [5, 6, 7, 8, 9, 10],
]
```

```
# Repeating the same value forever
[
    42 | repeat | isStream,
    42 | repeat | keepFirst(5) | toArray,
]
>> [
    true,
    [42, 42, 42, 42, 42],
]
```

```
# Explicitly creating a stream
myStream = (start) => newStream(
    value: () => start,
    next: () => myStream(start | times(2))
);
1 | myStream | keepFirst(5) | toArray
>> [1, 2, 4, 8, 16]
```

## Stream Collapsers

These functions exhaust an input stream to produce a scalar output or side effect. They loop forever if given an infinite stream.

```
# Indexing with a negative index
[
    ["foo", "bar", "baz"] @ -2,
    ["foo", "bar", "baz"] | at(-2),
    ["foo", "bar", "baz"] | at(-2, default: () => 42),
    ["foo", "bar", "baz"] | at(-4, default: () => 42),
    1 | to(5) @ -2,
    1 | to(5) | at(-2),
    1 | to(5) | at(-2, default: () => 42),
    1 | to(5) | at(-6, default: () => 42),
]
>> ["bar", "bar", "bar", 42, 4, 4, 4, 42]
```

```
# Last element
[
    ["foo"] | last,
    ["foo", "bar", "baz"] | last,
    1 | to(5) | last,
]
>> ["foo", "baz", 5]
```

```
# Sequence length
[
    length([]),
    length(["foo"]),
    length(["foo", "bar", "baz"]),
    length(1 | to(0)),
    length(1 | to(5)),
]
>> [0, 1, 3, 0, 5]
```

```
# Keeping trailing elements
[
    [42, 97, 6, 12, 64] | keepLast(3),
    1 | to(5) | keepLast(3),
]
>> [
    [6, 12, 64],
    [3, 4, 5],
]
```

```
# Dropping trailing elements
[
    [42, 97, 6, 12, 64] | dropLast,
    [42, 97, 6, 12, 64] | dropLast(3),
    1 | to(5) | dropLast,
    1 | to(5) | dropLast(3),
]
>> [
    [42, 97, 6, 12],
    [42, 97],
    [1, 2, 3, 4],
    [1, 2],
]
```

```
# Counting
[
    [1, 10, 2, 9, 3, 12] | count((i) => (i | isLessThan(10))),
    [1, 10, 2, 9, 3, 12] | toStream | count((i) => (i | isLessThan(10))),
]
>> [4, 4]
```

```
# True for all elements
[
    [1, 2, 3] | forAll((n) => (n | isLessThan(10))),
    [1, 42, 3] | forAll((n) => (n | isLessThan(10))),
    [1, 2, 3] | toStream | forAll((n) => (n | isLessThan(10))),
    [1, 42, 3] | toStream | forAll((n) => (n | isLessThan(10))),
]
>> [true, false, true, false]
```

```
# True for some elements
[
    [41, 2, 43] | forSome((n) => (n | isLessThan(10))),
    [41, 42, 43] | forSome((n) => (n | isLessThan(10))),
    [41, 2, 43] | toStream | forSome((n) => (n | isLessThan(10))),
    [41, 42, 43] | toStream | forSome((n) => (n | isLessThan(10))),
]
>> [true, false, true, false]
```

```
# Reversing
[
    ["foo", "bar", "spam", "eggs"] | reverse,
    ["foo", "bar", "spam", "eggs"] | toStream | reverse,
]
>> [
    ["eggs", "spam", "bar", "foo"],
    ["eggs", "spam", "bar", "foo"],
]
```

```
# Sorting in natural order
[
    ["foo", "bar", "spam", "eggs"] | sort,
    ["foo", "bar", "spam", "eggs"] | toStream | sort,
]
>> [
    ["bar", "eggs", "foo", "spam"],
    ["bar", "eggs", "foo", "spam"],
]
```

```
# Sorting arrays lexicographically
[[10, 2], [2, 1], [11, 3]] | sort
>> [[2, 1], [10, 2], [11, 3]]
```

```
# Sorting by sort key
["foo", "bar", "spam", "eggs"]
| sort(by: (word) => [word | length, word])
>> ["bar", "foo", "eggs", "spam"]
```

```
# Applying a side effect to each element
result = mutableArray();
["foo", "bar", "baz"] | forEach(result @ append:);
1 | to(5) | forEach(result @ append:);
result @ elements:()
>> ["foo", "bar", "baz", 1, 2, 3, 4, 5]
```

## Stream Accessors

These functions calculate a scalar value from a stream, but only access a finite number of elements to do so. Therefore, they are safe to call even on infinite streams.

```
# Is empty
[
    [] | isEmpty,
    [1] | isEmpty,
    [] | toStream | isEmpty,
    [1] | toStream | isEmpty,
    1 | repeat | isEmpty,
]
>> [
    true,
    false,
    true,
    false,
    false,
]
```

```
# Indexing with a positive index
[
    ["foo", "bar", "baz"] @ 2,
    ["foo", "bar", "baz"] | at(2),
    ["foo", "bar", "baz"] | at(2, default: () => 42),
    ["foo", "bar", "baz"] | at(0, default: () => 42),
    ["foo", "bar", "baz"] | at(4, default: () => 42),
    2 | to(5) @ 2,
    2 | to(5) | at(2),
    2 | to(5) | at(2, default: () => 42),
    2 | to(5) | at(0, default: () => 42),
    2 | to(5) | at(5, default: () => 42),
    2 | build(| times(2)) @ 2,
    2 | build(| times(2)) | at(2),
    2 | build(| times(2)) | at(2, default: () => 42),
    2 | build(| times(2)) | at(0, default: () => 42),
]
>> [
    "bar", "bar", "bar", 42, 42,
    3, 3, 3, 42, 42,
    4, 4, 4, 42,
]
```

```
# First element
[
    ["foo"] | first,
    ["foo", "bar", "baz"] | first,
    1 | to(5) | first,
    1 | build(| times(2)) | first,
]
>> ["foo", "foo", 1, 1]
```

## Stream Rebuilders

These functions create new streams that depend on existing ones, preserving stream laziness.

```
# Transforming
[
    [1, 2, 3] | transform((i) => times(i, i)) | toArray,
    1 | to(3) | transform((i) => times(i, i)) | toArray,
    1 | build(| up)
    | transform((i) => times(i, i))
    | keepFirst(3)
    | toArray,
]
>> [
    [1, 4, 9],
    [1, 4, 9],
    [1, 4, 9],
]
```

```
# Running state
[2, 8, 9, 3, 7]
| running(
    start: 0,
    next: (number, state: total) => (
        total | times(10) | plus(number)
    ),
)
| toArray
>> [0, 2, 28, 289, 2893, 28937]
```

```
# Keeping leading elements
[
    [42, 97, 6, 12, 64] | keepFirst(3) | toArray,
    1 | build(| times(2)) | keepFirst(3) | toArray,
]
>> [
    [42, 97, 6],
    [1, 2, 4],
]
```

```
# Dropping leading elements
[
    [42, 97, 6, 12, 64] | dropFirst | toArray,
    [42, 97, 6, 12, 64] | dropFirst(3) | toArray,
    1 | build(| times(2)) | dropFirst(3) | keepFirst(3) | toArray,
]
>> [
    [97, 6, 12, 64],
    [12, 64],
    [8, 16, 32],
]
```

```
# Slicing
[
    [42, 97, 6, 12, 64] | slice(from: 2, to: 4) | toArray,
    [42, 97, 6, 12, 64] | slice(from: 2, to: 10) | toArray,
    [42, 97, 6, 12, 64] | slice(from: 0, to: 4) | toArray,
    1 | build(| times(2)) | slice(from: 2, to: 4) | toArray,
]
>> [
    [97, 6, 12],
    [97, 6, 12, 64],
    [42, 97, 6, 12],
    [2, 4, 8]
]
```

The `while` function stops the stream as soon as the specified condition becomes false. The resulting stream contains only elements that satisfy the condition.

```
# While
1
| build(| times(2))
| while(| isLessThan(100))
| toArray
>> [1, 2, 4, 8, 16, 32, 64]
```

The `continueIf` function is like `while`, except it includes one extra element—the first element that doesn't satisfy the condition. This make some stopping conditions easier to express.

```
# Continue-If
1
| build(| times(2))
| continueIf(| isLessThan(100))
| toArray
>> [1, 2, 4, 8, 16, 32, 64, 128]
```

Conversely, the `thenRepeat` function adds endless copies of a constant value to the end of a finite sequence, useful if subsequent steps need to continue past the end of the stream.

```
# Then repeat
[2, 8, 9, 3, 7] | thenRepeat(null) | keepFirst(8) | toArray
>> [2, 8, 9, 3, 7, null, null, null]
```

```
# Operating on a sliding window
diffs = (sequence) => (
    sequence
    | sliding(2)
    | transform(([a, b]) => b | minus(a))
);
[
    [2, 8, 9, 3, 7] | diffs | toArray,
    [2, 8, 9, 3, 7] | repeat | flatten | diffs | keepFirst(6) | toArray,
]
>> [
    [6, 1, -6, 4],
    [6, 1, -6, 4, -5, 6],
]
```

```
# Filtering
[
    [1, 10, 2, 9, 3, 12] | where(| isLessThan(10)) | toArray,
    [1, 10, 2, 9, 3, 12]
    | repeat
    | flatten
    | where(| isLessThan(10))
    | keepFirst(5)
    | toArray,
]
>> [
    [1, 2, 9, 3],
    [1, 2, 9, 3, 1],
]
```

```
# Zipping
[
    [1, 2, 3] | zip(["one", "two", "three"]) | toArray,
    [1, 2, 3] | zip(["one", "two"]) | toArray,
    1 | build(| times(2)) | zip(["one", "two", "three"]) | toArray,
    1 | build(| times(2)) | zip(1 | build(| times(3))) | keepFirst(3) | toArray,
]
>> [
    [[1, "one"], [2, "two"], [3, "three"]],
    [[1, "one"], [2, "two"]],
    [[1, "one"], [2, "two"], [4, "three"]],
    [[1, 1], [2, 3], [4, 9]],
]
```

```
# Unzipping
[
    [[1, "one"], [2, "two"], [3, "three"]]
    | unzip
    | transform(| toArray)
    | toArray,
    [2, 2]
    | build(([a, b]) => [b | times(2), a | up])
    | unzip
    | transform(| keepFirst(4) | toArray)
    | toArray,
]
>> [
    [[1, 2, 3], ["one", "two", "three"]],
    [[2, 4, 6, 10], [2, 3, 5, 7]]
]
```

```
# Flattening nested sequences
[
    [[1], [2, 3], [4, 5, [6]]] | flatten | toArray,
    [[1], [2, 3], []] | flatten | toArray,
    [1] | build((a) => [*a, a | last | up]) | flatten | keepFirst(7) | toArray,
]
>> [
    [1, 2, 3, 4, 5, [6]],
    [1, 2, 3],
    [1, 1, 2, 1, 2, 3, 1],
]
```

```
# Dissecting
[
    [] | dissect(| isAtLeast(8)) | toArray,
    [2, 8, 9, 3, 7] | dissect(| isAtLeast(8)) | toArray,
    [2, 8, 9, 3, 7] | repeat | flatten | dissect(| isAtLeast(8)) | keepFirst(5) | toArray,
]
>> [
    [],
    [[2, 8], [9], [3, 7]],
    [[2, 8], [9], [3, 7, 2, 8], [9], [3, 7, 2, 8]],
]
```

```
# Chunking into arrays of a fixed size
[
    1 | to(9) | chunk(3) | toArray,
    1 | to(10) | chunk(3) | toArray,
    1 | to(11) | chunk(3) | toArray,
    1 | build(| times(2)) | chunk(3) | keepFirst(3) | toArray,
]
>> [
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11]],
    [[1, 2, 4], [8, 16, 32], [64, 128, 256]],
]
```

## Objects

```
# Object keys
object = {foo: "bar", spam: "eggs"};
object | keys
>> ["foo", "spam"]
```

```
# Object from properties
properties = [["foo", "bar"], ["spam", "eggs"]];
properties | toObject
>> {foo: "bar", spam: "eggs"}
```

```
# Indexing with default
object = {foo: "bar", spam: "eggs"};
[
    object | at("foo", default: () => "nothing"),
    object | at("baz", default: () => "nothing"),
]
>> ["bar", "nothing"]
```

```
# Array of properties
{foo: 1, bar: 2} | properties
>> [["foo", 1], ["bar", 2]]
```

```
# Merging
[{foo: 1, bar: 2}, {bar: 3, baz: 4}] | merge
>> {foo: 1, bar: 3, baz: 4}
```

## Sets and Maps

```
# Set
set = ["foo", "bar", "baz"] | newSet;
[
    set @ size:(),
    set @ elements:(),
    set @ has:("foo"),
    set @ has:("baz"),
    set @ has:("spam"),
]
>> [
    3,
    ["foo", "bar", "baz"],
    true,
    true,
    false,
]
```

```
# Collections as set keys
set = [["foo"], {foo: 42, bar: 97}] | newSet;
[
    set @ has:(["foo"]),
    set @ has:({foo: 42, bar: 97}),
    set @ has:({bar: 97, foo: 42}),
    set @ has:("foo"),
    set @ has:("[\"foo\"]"),
    set @ has:(["fob"]),
    set @ has:({foo: 42}),
    set @ has:({fob: 42, bar: 97}),
    set @ has:({foo: 43, bar: 97}),
]
>> [
    true,
    true,
    true,
    false,
    false,
    false,
    false,
    false,
    false,
]
```

```
# Map
map = [["foo", 42], ["bar", 97]] | newMap;
[
    map @ size:(),
    map @ keys:(),
    map @ values:(),
    map @ entries:(),
    map @ has:("foo"),
    map @ has:("spam"),
    map @ at:("foo"),
    map @ at:("bar", default: () => 216),
    map @ at:("spam", default: () => 216),
]
>> [
    2,
    ["foo", "bar"],
    [42, 97],
    [["foo", 42], ["bar", 97]],
    true,
    false,
    42,
    97,
    216,
]
```

```
# Collections as map keys
map = [[["foo"], 42], [{foo: 42, bar: 97}, 97]] | newMap;
[
    map @ has:(["foo"]),
    map @ has:({foo: 42, bar: 97}),
    map @ has:({bar: 97, foo: 42}),
    map @ has:("foo"),
    map @ has:("[\"foo\"]"),
    map @ has:(["fob"]),
    map @ has:({foo: 42}),
    map @ has:({fob: 42, bar: 97}),
    map @ has:({foo: 43, bar: 97}),
    map @ at:(["foo"]),
    map @ at:({bar: 97, foo: 42}),
]
>> [
    true,
    true,
    true,
    false,
    false,
    false,
    false,
    false,
    false,
    42,
    97,
]
```

```
# Grouping
[["foo", 42], ["bar", 97], ["foo", 216], ["foo", 729], ["spam", 57]] | group
>> [
    ["foo", [42, 216, 729]],
    ["bar", [97]],
    ["spam", [57]],
]
```

```
# Grouping with aggregation
[["foo", 42], ["bar", 97], ["foo", 216], ["foo", 729], ["spam", 57]]
| group(onGroup: length)
>> [
    ["foo", 3],
    ["bar", 1],
    ["spam", 1],
]
```

```
# Grouping by a key function
["foo", "bar", "spam", "eggs"] | groupBy(length)
>> [
    [3, ["foo", "bar"]],
    [4, ["spam", "eggs"]],
]
```

```
# Grouping by a key function with aggregation
["foo", "bar", "spam", "eggs"]
| groupBy(length, onGroup: least)
>> [
    [3, "bar"],
    [4, "eggs"],
]
```

## Mutable Objects

```
# Variable
var = variable(42);
[
    var @ get:(),
    var @ set:(73),
    var @ get:(),
]
>> [42, 73, 73]
```

```
# Mutable array
array = ["foo", "bar", "baz"] | mutableArray
@ append:("spam")
@ append:("eggs")
@ append:("foo")
@ set:(2, "toast")
@ storeAt:("sausages", 4);
[
    array @ size:(),
    array @ elements:(),
    array @ at:(1),
    array @ at:(1, default: () => "boo"),
    array @ at:(7, default: () => "boo"),
    array @ at:(-2),
    array @ pop:(),
    array @ size:(),
    array @ elements:(),
]
>> [
    6,
    ["foo", "toast", "baz", "sausages", "eggs", "foo"],
    "foo",
    "foo",
    "boo",
    "eggs",
    "foo",
    5,
    ["foo", "toast", "baz", "sausages", "eggs"]
]
```

```
# Mutable set
set = ["foo", "bar", "baz"] | mutableSet
@ add:("spam")
@ add:("eggs")
@ add:("foo")
@ remove:("bar")
@ remove:("quux");
[
    set @ size:(),
    set @ elements:(),
    set @ has:("foo"),
    set @ has:("bar"),
    set @ has:("baz"),
    set @ has:("spam"),
    set @ has:("gorp"),
]
>> [
    4,
    ["foo", "baz", "spam", "eggs"],
    true,
    false,
    true,
    true,
    false,
]
```

```
# Collections as mutable set keys
set = [["foo"]] | mutableSet;
[
    set @ has:(["foo"]),
    set @ has:("foo"),
    set @ has:("[\"foo\"]"),
    set @ has:(["fob"]),
    set
    @ add:({foo: 42, bar: 97})
    @ has:({foo: 42, bar: 97}),
    set @ has:({bar: 97, foo: 42}),
    set @ has:({fob: 42, bar: 97}),
    set @ has:({foo: 43, bar: 97}),
]
>> [
    true,
    false,
    false,
    false,
    true,
    true,
    false,
    false,
]
```

```
# Mutable map
map = [["foo", 42], ["bar", 97]] | mutableMap
@ set:("eggs", 216)
@ storeAt:(729, "foo")
@ remove:("bar");
[
    map @ size:(),
    map @ keys:(),
    map @ values:(),
    map @ entries:(),
    map @ has:("foo"),
    map @ has:("bar"),
    map @ at:("foo"),
    map @ at:("eggs", default: () => 57),
    map @ at:("bar", default: () => 57),
]
>> [
    2,
    ["foo", "eggs"],
    [729, 216],
    [["foo", 729], ["eggs", 216]],
    true,
    false,
    729,
    216,
    57,
]
```

```
# Collections as mutable map keys
map = [[["foo"], 42]] | mutableMap;
[
    map @ has:(["foo"]),
    map @ has:("foo"),
    map @ has:("[\"foo\"]"),
    map @ has:(["fob"]),
    map @ at:(["foo"]),
    map
    @ set:({foo: 42, bar: 97}, 97)
    @ has:({foo: 42, bar: 97}),
    map @ has:({bar: 97, foo: 42}),
    map @ has:({fob: 42, bar: 97}),
    map @ has:({foo: 43, bar: 97}),
    map @ at:({bar: 97, foo: 42}),
]
>> [
    true,
    false,
    false,
    false,
    42,
    true,
    true,
    false,
    false,
    97,
]
```

```
# Doing side effects on an expression result as it flies by
array = mutableArray();
[
    42 | also((n) => array @ append:(n)),
    array @ elements:(),
]
>> [42, [42]]
```

## Errors

```
# Creation and conversion to object
{error: errorType, details:} = error("badIdea", foo: "bar", spam: "eggs") | toObject;
[errorType, details]
>> ["badIdea", {foo: "bar", spam: "eggs"}]
```

## Validation

```
# Matching typeOf results
[
    null | matches("null"),
    false | matches("null"),
    false | matches("boolean"),
    42 | matches("boolean"),
    42 | matches("number"),
    "foo" | matches("number"),
    "foo" | matches("string"),
    [1, 2, 3] | matches("string"),
    [1, 2, 3] | matches("array"),
    {foo: 1, bar: 2} | matches("array"),
    {foo: 1, bar: 2} | matches("object"),
    typeOf | matches("object"),
    typeOf | matches("builtin"),
    ((x) => x) | matches("builtin"),
    ((x) => x) | matches("given"),
    (1 @ 1)! |  matches("given"),
    (1 @ 1)! | matches("error"),
    null | matches("error"),
]
>> [
    true,
    false,
    true,
    false,
    true,
    false,
    true,
    false,
    true,
    false,
    true,
    false,
    true,
    false,
    true,
    false,
    true,
    false,
]
```

```
# Matching the supertypes "function", "sequence", and "any"
[
    "foo" | matches("function"),
    [1, 2, 3] | matches("function"),
    {foo: 1, bar: 2} | matches("function"),
    typeOf | matches("function"),
    ((x) => x) | matches("function"),
    (1 @ 1)! | matches("function"),
    "foo" | matches("sequence"),
    [1, 2, 3] | matches("sequence"),
    {foo: 1, bar: 2} | matches("sequence"),
    typeOf | matches("sequence"),
    ((x) => x) | matches("sequence"),
    (1 @ 1)! | matches("sequence"),
    "foo" | matches("any"),
    [1, 2, 3] | matches("any"),
    {foo: 1, bar: 2} | matches("any"),
    typeOf | matches("any"),
    ((x) => x) | matches("any"),
    (1 @ 1)! | matches("any"),
]
>> [
    false,
    false,
    false,
    true,
    true,
    false,
    true,
    true,
    false,
    false,
    false,
    false,
    true,
    true,
    true,
    true,
    true,
    true,
]
```

```
# Matching a type with a predicate
sch = is("number", where: (n) => isLessThan(n, 10));
[
    1 | matches(sch),
    42 | matches(sch),
    "foo" | matches(sch),
]
>> [
    true,
    false,
    false,
]
```

```
# Matching a list of specific values
sch = oneOf("red", "green", "blue");
[
    "red" | matches(sch),
    "blue" | matches(sch),
    "foo" | matches(sch),
    42 | matches(sch),
]
>> [
    true,
    true,
    false,
    false,
]
```

```
# Matching an array where all elements must be of a given type
sch = arrayOf("string");
[
    [] | matches(sch),
    ["foo", "bar"] | matches(sch),
    [1, 2, 3] | matches(sch),
    ["foo", 1] | matches(sch),
    42 | matches(sch),
]
>> [
    true,
    true,
    false,
    false,
    false,
]
```

```
# Matching an array where each element has its own schema
sch = tupleLike(["string", "number", arrayOf("number")]);
[
    ["foo", 42, [1, 2, 3]] | matches(sch),
    ["foo", 42, [], "extra"] | matches(sch),
    ["foo", "bar", [1, 2, 3]] | matches(sch),
    ["foo", 42] | matches(sch),
    42 | matches(sch),
]
>> [
    true,
    true,
    false,
    false,
    false,
]
```

```
# Matching an object where keys and values must match schemas
sch = objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number");
[
    {} | matches(sch),
    {x: 42} | matches(sch),
    {x: 0, y: 0, z: 0} | matches(sch),
    {x: 0, y: 0, za: 0} | matches(sch),
    {x: 0, y: 0, "": 0} | matches(sch),
    {x: 0, y: 0, z: "foo"} | matches(sch),
    42 | matches(sch),
]
>> [
    true,
    true,
    true,
    false,
    false,
    false,
    false,
]
```

```
# Matching an object with a specific structure
person = recordLike({name: "string", age: optional("number")});
[
    {name: "John", age: 42} | matches(person),
    {name: "John"} | matches(person),
    {name: "John", age: 42, job: "doctor"} | matches(person),
    {name: "John", age: "middle"} | matches(person),
    {name: 42} | matches(person),
    {age: 42} | matches(person),
    42 | matches(person),
]
>> [
    true,
    true,
    true,
    false,
    false,
    false,
    false,
]
```

```
# Matching a union
sch = either("string", "number");
[
    "foo" | matches(sch),
    42 | matches(sch),
    [1, 2, 3] | matches(sch),
]
>> [
    true,
    true,
    false,
]
```
