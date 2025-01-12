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
[3 | increment, -3 | increment, 1.5 | increment]
>> [4, -2, 2.5]
```

```
# Decrement
[3 | decrement, -3 | decrement, 1.5 | decrement]
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
    "foobar" | slice(2 | to(4)),
    "foobar" | slice(2 | to(10)),
    "foobar" | slice(0 | to(4)),
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
    toString(1 | to(4)),
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
    "stream [1, 2, 3]",
    "stream [1, 2, 3...]",
    "{foo: \"bar\", \"spam!\": \"eggs\"}",
    "function toString",
    "error wrongType {value: 1, expectedType: {either: [\"string\", \"array\", \"object\"]}}"
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

The `repeat` function repeatedly updates a value by applying the specified `next` function. The function is applied until the next application would cause the `while` function to return `false`, i.e. the result is the last value for which `while` returns `true`.

```
# Repeat with while
powerOfTwo = (limit) => repeat(
    1,
    while: (n) => n | isLessThan(limit),
    next: (n) => n | times(2),
);
[
    powerOfTwo(1),
    powerOfTwo(3),
    powerOfTwo(1000),
]
>> [
    1,
    2,
    512,
]
```

Alternatively, you can pass a `continueIf` function. Then the result is the first value for which `continueIf` returns `false`.

```
# Repeat with continue-if
powerOfTwo = (limit) => repeat(
    1,
    next: (n) => n | times(2),
    continueIf: (n) => n | isLessThan(limit),
);
[
    powerOfTwo(1),
    powerOfTwo(3),
    powerOfTwo(1000),
]
>> [
    1,
    4,
    1024,
]
```

```
# Repeat - next isn't called if continue-if is false
mySum = (array) => [1, 0] | repeat(
    next: (state) => (
        [i, total] = state;
        [i | increment, total | plus(array @ i)]
    ),
    continueIf: (state) => (
        [i, total] = state;
        i | isAtMost(array | length)
    ),
) @ 2;
mySum([1, 2, 3])
>> 6
```

## Stream Builders

These functions build up new streams from scalar inputs.

The `build` function generates a stream by repeatedly applying a function to a start value.

```
# Build
powersOfTwo = 1 | build(| times(2));
[
    powersOfTwo @ 1,
    powersOfTwo @ 3,
    powersOfTwo @ 8,
]
>> [1, 4, 128]
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

## Stream Collapsers

These functions exhaust an input stream to produce a scalar output or side effect. They loop forever if given an infinite stream.

The `collapse` function combines each element of the input stream with an internal state in turn, returning the final state.

```
# Collapse
fromBinary = (digits) => digits | collapse(
    start: 0,
    next: (state, digit) => state | times(2) | plus(digit),
);
[1, 0, 1, 0, 1, 0] | fromBinary
>> 42
```

The `collapse` function automatically stops if it reaches the end of the input stream, but it also accepts the same `while` and `continueIf` arguments that `repeat` and `build` do, allowing you to specify additional stopping criteria.

```
# Collapse with while
blackjackSafe = (cards) => cards | collapse(
    start: 0,
    while: (state) => state | isAtMost(21),
    next: (state, card) => state | plus(card),
);
[2, 8, 9, 3, 7] | blackjackSafe
>> 19
```

```
# Collapse with continueIf
blackjackBust = (cards) => cards | collapse(
    start: 0,
    next: (state, card) => state | plus(card),
    continueIf: (state) => state | isAtMost(21),
);
[2, 8, 9, 3, 7] | blackjackBust
>> 22
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
# Transposing
[
    [[1, "one"], [2, "two"], [3, "three"]] | transpose,
    [[1, "one"], [2, "two"], [3, "three"]] | toStream | transpose,
]
>> [
    [[1, 2, 3], ["one", "two", "three"]],
    [[1, 2, 3], ["one", "two", "three"]],
]
```

```
# Transposing - ragged arrays
[[42, 97, 216, 729], ["foo", "bar"], ["spam", "eggs", "cheese"]] | transpose
>> [[42, "foo", "spam"], [97, "bar", "eggs"]]
```

```
# Transposing - filling missing elements
[[42, 97, 216, 729], ["foo", "bar"], ["spam", "eggs", "cheese"]]
| transpose(fillWith: (arrayNumber:, elementNumber:) => [arrayNumber, elementNumber])
>> [
    [42, "foo", "spam"],
    [97, "bar", "eggs"],
    [216, [2, 3], "cheese"],
    [729, [2, 4], [3, 4]],
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
    ["foo", "bar", "spam", "eggs"] | sort,
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

## Stream Accessors

These functions calculate a scalar value from a stream, but only access a finite number of elements to do so. Therefore, they are safe to call even on infinite streams.

## Stream Rebuilders

These functions create new streams that depend on existing ones, preserving stream laziness.

```
# Transforming
[1, 2, 3] | transform((i) => times(i, i)) | toArray
>> [1, 4, 9]
```

```
# Transforming with state
[2, 8, 9, 3, 7]
| transform((number, back: index) => number | times(index))
| withState(start: 1, next: | times(2))
| toArray
>> [2, 16, 36, 24, 112]
```

```
# Transforming with running
[2, 8, 9, 3, 7]
| transform((number, back: total) => total | times(10) | plus(number))
| withRunning(start: 0)
| toArray
>> [0, 2, 28, 289, 2893, 28937]
```

```
# Keeping leading elements
[
    [42, 97, 6, 12, 64] | keepFirst(3) | isStream,
    [42, 97, 6, 12, 64] | keepFirst(3) | toArray,
    1 | build(| times(2)) | keepFirst(3) | toArray,
]
>> [
    true,
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
# Slicing arrays
[
    [42, 97, 6, 12, 64] | slice(2 | to(4)),
]
>> [
    [97, 6, 12],
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

```
# Filtering
[1, 10, 2, 9, 3, 12] | where((i) => (i | isLessThan(10))) | toArray
>> [1, 2, 9, 3]
```

```
# Zipping
[
    [1, 2, 3] | zip(["one", "two", "three"]),
    [1, 2, 3] | zip(["one", "two"]),
] | toArray
>> [
    [[1, "one"], [2, "two"], [3, "three"]],
    [[1, "one"], [2, "two"]],
]
```

```
# Zipping - filling missing elements
zipFilling = (*args) => (
    zip(
        *args,
        fillWith: ((arrayNumber:, elementNumber:) => [arrayNumber, elementNumber])
    )
    | toArray
);
[
    [1, 2, 3] | zipFilling(["one", "two", "three"]),
    [1, 2, 3] | zipFilling(["one", "two"]),
]
>> [
    [[1, "one"], [2, "two"], [3, "three"]],
    [[1, "one"], [2, "two"], [3, [2, 3]]],
]
```

```
# Flattening nested arrays
[[1], [2, 3], [4, 5, [6]]] | flatten | toArray
>> [1, 2, 3, 4, 5, [6]]
```

```
# Chunking into arrays of a fixed size
[
    1 | to(9) | chunk(3) | toArray,
    1 | to(10) | chunk(3) | toArray,
    1 | to(11) | chunk(3) | toArray,
]
>> [
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11]],
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
