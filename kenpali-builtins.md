# Kenpali Builtins Specification

Builtins are functions that must be provided by the host platform.

## Arithmetic

```
# Plus
[plus(), plus(1), plus(1, 2), plus(1, 2, 3)]
>> [0, 1, 3, 6]
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

## Strings

```
# String length
[length(""), length("f"), length("foo")]
>> [0, 1, 3]
```

```
# Indexing strings
string = "foobar"
[
    string @ 1,
    string @ 4,
    string @ 6,
]
>> ["f", "b", "r"]
```

```
# Indexing strings with escapes
string = "\"\\\/\b\f\n\r\t\u1234"
[
    string @ 1,
    string @ 5,
    string @ 9,
]
>> ["\"", "\f", "\u1234"]
```

```
# Joining strings
[
    join([]),
    join(["foo"]),
    join(["foo", "bar", "baz"]),
    join(["foo", "bar", "baz"], with: "|"),
]
>> ["", "foo", "foobarbaz", "foo|bar|baz"]
```

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
    toNumber(42),
]
>> [1, -2.5, 42]
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
    "error wrongArgumentType {value: 1, expectedType: {either: [\"sequence\", \"object\"]}}"
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
# Switch
twiddle = (x) => switch(
    x,
    ["string" | as("s"), (s:) => join(["Hello, ", s, "!"])],
    ["number" | as("n"), (n:) => (n | plus(5))],
    ["any", null]
);
[
    twiddle("world"),
    twiddle(42),
    twiddle([]),
]
>> ["Hello, world!", 47, null]
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

The `build` function generates an array by repeatedly applying a function to a start value, and transforming the resulting states into array elements.

As with `repeat`, there are two ways of specifying when the loop should end. If you pass a `while` function, the result array will contain only elements produced from states for which the `while` function returns `true`.

```
# Build
fib = (limit) => build(
    [1, 1],
    while: (state) => state @ 1 | isLessThan(limit),
    out: (state) => [state @ 1],
    next: (state) => [state @ 2, sum(state)],
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
    next: (state) => [state @ 2, sum(state)],
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
        state @ 1 | isDivisibleBy(2),
        then: () => [],
        else: () => [state @ 1, state @ 1],
    ),
    next: (state) => [state @ 2, sum(state)],
)
>> [1, 1, 1, 1, 3, 3, 5, 5, 13, 13]
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

## Errors

```
# Creation and conversion to object
error("badIdea", foo: "bar", spam: "eggs") | toObject
>> {error: "badIdea", details: {foo: "bar", spam: "eggs"}}
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
