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
# Array of characters in a string
characters("foobar")
>> ["f", "o", "o", "b", "a", "r"]
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
    typeOf({foo: 1, bar: 2}),
    typeOf(typeOf),
    typeOf((x) => x),
    typeOf((1 @ 1)!),
]
>> ["null", "boolean", "boolean", "number", "string", "array", "object", "builtin", "given", "error"]
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
    isNull({foo: 1, bar: 2}),
    isNull(typeOf),
    isNull((x) => x),
    isNull((1 @ 1)!),
]
>> [true, false, false, false, false, false, false, false, false, false]
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
    isBoolean({foo: 1, bar: 2}),
    isBoolean(typeOf),
    isBoolean((x) => x),
    isBoolean((1 @ 1)!),
]
>> [false, true, true, false, false, false, false, false, false, false]
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
    isNumber({foo: 1, bar: 2}),
    isNumber(typeOf),
    isNumber((x) => x),
    isNumber((1 @ 1)!),
]
>> [false, false, false, true, false, false, false, false, false, false]
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
    isString({foo: 1, bar: 2}),
    isString(typeOf),
    isString((x) => x),
    isString((1 @ 1)!),
]
>> [false, false, false, false, true, false, false, false, false, false]
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
    isArray({foo: 1, bar: 2}),
    isArray(typeOf),
    isArray((x) => x),
    isArray((1 @ 1)!),
]
>> [false, false, false, false, false, true, false, false, false, false]
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
    isObject({foo: 1, bar: 2}),
    isObject(typeOf),
    isObject((x) => x),
    isObject((1 @ 1)!),
]
>> [false, false, false, false, false, false, true, false, false, false]
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
    isBuiltin({foo: 1, bar: 2}),
    isBuiltin(typeOf),
    isBuiltin((x) => x),
    isBuiltin((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, true, false, false]
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
    isGiven({foo: 1, bar: 2}),
    isGiven(typeOf),
    isGiven((x) => x),
    isGiven((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, false, true, false]
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
    isError({foo: 1, bar: 2}),
    isError(typeOf),
    isError((x) => x),
    isError((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, false, false, true]
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
    isFunction({foo: 1, bar: 2}),
    isFunction(typeOf),
    isFunction((x) => x),
    isFunction((1 @ 1)!),
]
>> [false, false, false, false, false, false, false, true, true, false]
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
    isSequence({foo: 1, bar: 2}),
    isSequence(typeOf),
    isSequence((x) => x),
    isSequence((1 @ 1)!),
]
>> [false, false, false, false, true, true, false, false, false, false]
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

## Arrays

```
# Array length
[
    length([]),
    length(["foo"]),
    length(["foo", "bar", "baz"]),
]
>> [0, 1, 3]
```

The `build` function generates an array by repeatedly applying a function to a start value, and transforming the resulting states into array elements.

As with `repeat`, there are two ways of specifying when the loop should end. If you pass a `while` function, the result array will contain only elements produced from states for which the `while` function returns `true`.

```
# Build
fib = (limit) => build(
    [1, 1],
    while: (state) => state @ 1 | isLessThan(limit),
    out: (state) => [state @ 1],
    next: (state) => [state @ 2, plus(*state)],
);
[
    fib(1),
    fib(2),
    fib(20),
]
>> [
    [],
    [1, 1],
    [1, 1, 2, 3, 5, 8, 13]
]
```

Or you can pass a `continueIf` function, in which case the result array will contain the elements produced from the first state for which `continueIf` returns `false`.

```
# Build with continue-if
fib = (limit) => build(
    [1, 1],
    out: (state) => [state @ 1],
    next: (state) => [state @ 2, plus(*state)],
    continueIf: (state) => state @ 1 | isLessThan(limit),
);
[
    fib(1),
    fib(2),
    fib(20),
]
>> [
    [1],
    [1, 1, 2],
    [1, 1, 2, 3, 5, 8, 13, 21]
]
```

Since the `out` property is an array, an iteration can add multiple values to the result, or skip adding values entirely.

```
# Build with multiple outs
build(
    [1, 1],
    while: (state) => state @ 1 | isLessThan(20),
    out: (state) => if(
        state @ 1 | divideWithRemainder(2) @ remainder: | equals(0),
        then: () => [],
        else: () => [state @ 1, state @ 1],
    ),
    next: (state) => [state @ 2, plus(*state)],
)
>> [1, 1, 1, 1, 3, 3, 5, 5, 13, 13]
```

```
# Ranges
[
    1 | to(5),
    5 | to(10),
]
>> [
    [1, 2, 3, 4, 5],
    [5, 6, 7, 8, 9, 10],
]
```

```
# Ranges with step
[
    1 | to(10, by: 3),
    5 | to(10, by: 2),
]
>> [
    [1, 4, 7, 10],
    [5, 7, 9],
]
```

```
# Ranges with negative step
[
    5 | to(1, by: -1),
    10 | to(5, by: -2),
]
>> [
    [5, 4, 3, 2, 1],
    [10, 8, 6],
]
```

```
# Ranges defined by size
[
    1 | toSize(5),
    5 | toSize(6),
]
>> [
    [1, 2, 3, 4, 5],
    [5, 6, 7, 8, 9, 10],
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

```
# Dropping leading and trailing elements
[
    [42, 97, 6, 12, 64] | dropFirst,
    [42, 97, 6, 12, 64] | dropFirst(3),
    [42, 97, 6, 12, 64] | dropLast,
    [42, 97, 6, 12, 64] | dropLast(3),
]
>> [
    [97, 6, 12, 64],
    [12, 64],
    [42, 97, 6, 12],
    [42, 97],
]
```

```
# Rebuilding
[1, 2, 3] | rebuild((i) => [i, times(i, i)])
>> [1, 1, 2, 4, 3, 9]
```

```
# Transforming
[1, 2, 3] | transform((i) => times(i, i))
>> [1, 4, 9]
```

```
# Filtering
[1, 10, 2, 9, 3, 12] | where((i) => (i | isLessThan(10)))
>> [1, 2, 9, 3]
```

```
# Zipping
[
    [1, 2, 3] | zip(["one", "two", "three"]),
    [1, 2, 3] | zip(["one", "two"]),
]
>> [
    [[1, "one"], [2, "two"], [3, "three"]],
    [[1, "one"], [2, "two"]],
]
```

```
# Unzipping
[[1, "one"], [2, "two"], [3, "three"]] | unzip
>> [[1, 2, 3], ["one", "two", "three"]]
```

```
# Counting
[1, 10, 2, 9, 3, 12] | count((i) => (i | isLessThan(10)))
>> 4
```

```
# True for all elements
[
    [1, 2, 3] | forAll((n) => (n | isLessThan(10))),
    [1, 42, 3] | forAll((n) => (n | isLessThan(10))),
]
>> [true, false]
```

```
# True for some elements
[
    [41, 2, 43] | forSome((n) => (n | isLessThan(10))),
    [41, 42, 43] | forSome((n) => (n | isLessThan(10))),
]
>> [true, false]
```

```
# Flattening nested arrays
[[1], [2, 3], [4, 5, [6]]] | flatten
>> [1, 2, 3, 4, 5, [6]]
```

```
# Chunking into arrays of a fixed size
[
    1 | to(9) | chunk(3),
    1 | to(10) | chunk(3),
    1 | to(11) | chunk(3),
]
>> [
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11]],
]
```

```
# Reversing
["foo", "bar", "spam", "eggs"] | reverse
>> ["eggs", "spam", "bar", "foo"]
```

```
# Sorting in natural order
["foo", "bar", "spam", "eggs"] | sort
>> ["bar", "eggs", "foo", "spam"]
```

```
# Sorting by sort key
["foo", "bar", "spam", "eggs"]
| sort(by: (word) => [word | length, word])
>> ["bar", "foo", "eggs", "spam"]
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
    object | at("foo", default: "nothing"),
    object | at("baz", default: "nothing"),
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
    map @ at:("bar", default: 216),
    map @ at:("spam", default: 216),
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
    array @ at:(-2),
    array @ pop:(),
    array @ size:(),
    array @ elements:(),
]
>> [
    6,
    ["foo", "toast", "baz", "sausages", "eggs", "foo"],
    "foo",
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
    map @ at:("eggs", default: 57),
    map @ at:("bar", default: 57),
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
