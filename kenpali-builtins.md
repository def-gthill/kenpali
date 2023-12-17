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
# And short-circuiting
[
    and(false, [] @ 1),
    and(false, (foo = foo; foo)),
]
>> [false, false]
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
# Or short-circuiting
[
    or(true, [] @ 1),
    or(true, (foo = foo; foo)),
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
>> ["null", "boolean", "boolean", "number", "string", "array", "record", "builtin", "given", "error"]
```

The `typeOf` function never returns `"object"` or `"function"`, since these terms aren't specific enough. The term "object" includes records, givens, and errors (i.e. anything that can be serialized to a JSON object), while the term "function" includes builtins and givens (i.e. anything that can be called).

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

```
# If short-circuiting
[
    if(true, then: 1, else: [] @ 1),
    if(true, then: 1, else: (foo = foo; foo)),
    if(false, then: [] @ 1, else: 2),
    if(false, then: (foo = foo; foo), else: 2),
]
>> [1, 1, 2, 2]
```

The `repeat` function repeatedly updates a value by applying the specified function. When the `while` property becomes `false`, the `next` property is ignored, and the final result is the *previous* value.

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

In contrast, when the `continueIf` property becomes `false`, the `next` property *is* the final result.

```
# Repeat with Continue-If
repeat(
    1,
    (previous) => (
        next = previous | times(2);
        {
            next: next,
            continueIf: next | isLessThan(1000),
        }
    )
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

The `build` function generates an array by repeatedly applying a function to a start value. When the `while` property becomes `false`, the `out` and `next` properties are ignored; the final array will contain all *previous* values of `out`.

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

In contrast, when the `continueIf` property becomes `false`, this `out` value will be the *last* value added to the array.

```
# Build with Continue-If
build(
    [1, 1],
    (previous) => {
        out: previous @ 1,
        next: [previous @ 2, plus(previous @ 1, previous @ 2)],
        continueIf: previous @ 2 | isLessThan(20),
    }
)
>> [1, 1, 2, 3, 5, 8, 13]
```

A `where` boolean property can also be provided; if
it's false, this `out` value is skipped, but the internal state is still updated.

```
# Build with Where
build(
    [1, 1],
    (previous) => {
        while: previous @ 1 | isLessThan(20),
        out: previous @ 1,
        next: [previous @ 2, plus(previous @ 1, previous @ 2)],
        where: previous @ 1 | isDivisibleBy(2) | not,
    }
)
>> [1, 1, 3, 5, 13]
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
    {foo: 1, bar: 2} | matches("record"),
    typeOf | matches("record"),
    typeOf | matches("builtin"),
    (x) => x | matches("builtin"),
    (x) => x | matches("given"),
    matches((1 @ 1)!, "given"),
    matches((1 @ 1)!, "error"),
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
# Matching the supertypes "object", "function", "sequence", and "any"
[
    "foo" | matches("object"),
    [1, 2, 3] | matches("object"),
    {foo: 1, bar: 2} | matches("object"),
    typeOf | matches("object"),
    (x) => x | matches("object"),
    matches((1 @ 1)!, "object"),
    "foo" | matches("function"),
    [1, 2, 3] | matches("function"),
    {foo: 1, bar: 2} | matches("function"),
    typeOf | matches("function"),
    (x) => x | matches("function"),
    matches((1 @ 1)!, "function"),
    "foo" | matches("sequence"),
    [1, 2, 3] | matches("sequence"),
    {foo: 1, bar: 2} | matches("sequence"),
    typeOf | matches("sequence"),
    (x) => x | matches("sequence"),
    matches((1 @ 1)!, "sequence"),
    "foo" | matches("any"),
    [1, 2, 3] | matches("any"),
    {foo: 1, bar: 2} | matches("any"),
    typeOf | matches("any"),
    (x) => x | matches("any"),
    matches((1 @ 1)!, "any"),
]
>> [
    false,
    false,
    true,
    false,
    true,
    true,
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
sch = ["string", "number", arrayOf("number")];
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
person = {name: "string", age: optional("number")};
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
