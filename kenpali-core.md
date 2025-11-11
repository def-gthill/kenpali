# Kenpali Core Specification

The Core functions must be available to any Kenpali program. Reference implementations for some of them in terms of the others are provided by `core.kpc`, enabling a complete implementation with fewer platform functions.

## Arithmetic|arithmetic

### add|add

Returns the sum of its arguments

Parameters:

- `*numbers` (_Array of Number_): The numbers to add up.

Returns:

- (_Number_): The sum.


```
# Addition
[add(), add(1), add(1, 2), add(1, 2, 3)]
>> [0, 1, 3, 6]
```

### sum|sum

Returns the sum of the values in the specified sequence.

Parameters:

- `numbers` (_Sequence of Number_): The numbers to add up.

Returns:

- (_Number_): The sum.

```
# Sum
sum([1, 2, 3, 4, 5])
>> 15
```

### sub|sub

Returns its first argument minus its second argument.

Parameters:

- `a` (_Number_): The amount to subtract from.
- `b` (_Number_): The amount to take away.

Returns:

- (_Number_): The difference.

```
# Subtraction
[5 | sub(2), 2 | sub(5)]
>> [3, -3]
```

### negative|negative

Returns its argument with the sign flipped.

Parameters:

- `n` (_Number_): The value to negate.

Returns:

- (_Number_): The negated value.

```
# Negative
[negative(42), negative(-1.5)]
>> [-42, 1.5]
```

### absolute|absolute

Returns its argument with the sign set to positive.

Parameters:

- `n` (_Number_): The value to make positive.

Returns:

- (_Number_): The absolute value.

```
# Absolute value
[
    42 | absolute,
    -42 | absolute,
]
>> [42, 42]
```

### up|up

Returns its argument plus one.

Parameters:

- `n` (_Number_): The value to increase.

Returns:

- (_Number_): The incremented value.

```
# Increment
[3 | up, -3 | up, 1.5 | up]
>> [4, -2, 2.5]
```

### down|down

Returns its argument minus one.

Parameters:

- `n` (_Number_): The value to decrease.

Returns:

- (_Number_): The decremented value.

```
# Decrement
[3 | down, -3 | down, 1.5 | down]
>> [2, -4, 0.5]
```

### mul|mul

Returns the product of its arguments.

Parameters:

- `*numbers` (_Array of Number_): The values to multiply.

Returns:

- (_Number_): The product.

```
# Multiplication
[mul(), mul(2), mul(2, 3), mul(2, 3, 4)]
>> [1, 2, 6, 24]
```

### div|div

Returns its first argument divided by its second argument.

Parameters:

- `a` (_Number_): The dividend.
- `b` (_Number_): The divisor.

Returns:

- (_Number_): The quotient.

```
# Division
[12 | div(3), 12 | div(8)]
>> [4, 1.5]
```

### oneOver|oneOver

Returns the reciprocal of its argument.

Parameters:

- `x` (_Number_): The value to invert.

Returns:

- (_Number_): The reciprocal.

```
# One over
[oneOver(2), oneOver(-0.5)]
>> [0.5, -2]
```

### quotientBy|quotientBy

Returns the integer quotient of its first argument divided by its second argument.

Parameters:

- `a` (_Number_): The dividend.
- `b` (_Number_): The divisor.

Returns:

- (_Number_): The integer quotient.

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

### remainderBy|remainderBy

Returns the remainder of its first argument divided by its second argument.

A non-zero remainder always has the same sign as the divisor.

Parameters:

- `a` (_Number_): The dividend.
- `b` (_Number_): The divisor.

Returns:

- (_Number_): The remainder.

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

### isDivisibleBy|isDivisibleBy

Returns whether its first argument is divisible by its second argument.

Parameters:

- `a` (_Number_): The dividend.
- `b` (_Number_): The divisor.

Returns:

- (_Boolean_): Whether `a` divided by `b` is an integer.

```
# Divisible by
[
    10 | isDivisibleBy(5),
    10 | isDivisibleBy(3),
    9 | isDivisibleBy(3),
]
>> [true, false, true]
```

## Strings|strings

### toCodePoints|toCodePoints

Returns an array of Unicode code points representing the characters in the given string.

Parameters:

- `string` (_String_): The text to convert.

Returns:

- (_Array of Number_): The code points.

```
# Converting a string to code points
toCodePoints("foo\u1234\u{1f61b}")
>> [102, 111, 111, 4660, 128539]
```

### fromCodePoints|fromCodePoints

Returns a string created from the given array of Unicode code points.

Parameters:

- `codePoints` (_Array of Number_): The code points to convert.

Returns:

- (_String_): The resulting text.

```
# Converting code points to a string
fromCodePoints([102, 111, 111, 4660])
>> "foo\u1234"
```

### join|join

Returns a string created by joining the given values with a separator.

Parameters:

- `strings` (_Array or Stream of String_): The values to join.
- `on:` (_String_, default `""`): The separator to insert between values.

Returns:

- (_String_): The joined string.

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

### joinLines|joinLines

Returns a string created by joining the given values with newlines.

Parameters:

- `strings` (_Array or Stream of String_): The values to join.

Returns:

- (_String_): The joined string.

```
# Joining lines
["foo", "bar", "", "baz"] | joinLines
>> "foo\nbar\n\nbaz"
```

### split|split

Returns an array of substrings by splitting the given string at occurrences of a separator.

Parameters:

- `string` (_String_): The text to split.
- `on:` (_String_): The separator to split on.

Returns:

- (_Array of String_): The split substrings.

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

### splitLines|splitLines

Returns an array of the newline-separated lines in the specified string.

Parameters:

- `string` (_String_): The text to split.

Returns:

- (_Array of String_): The lines.

```
# Splitting lines
"foo\nbar\n\nbaz" | splitLines
>> ["foo", "bar", "", "baz"]
```

## Comparison|comparison

### Comparison Rules|comparison-rules

Kenpali can perform equality and ordering comparisons on various types.

All comparisons are strict about types: testing values of different types for equality always returns `false`, while using ordering comparisons on different types always throws a `wrongArgumentType` error.

Any two values can be tested for equality. To be considered equal, the values must have the same type, _and_:

- If both are booleans, they must both be true or both be false.
- If both are numbers, they must have the same numerical value.
- If both are strings, they must have the same sequence of Unicode code points.
- If both are arrays, they must have the same length, and corresponding elements must be equal according to the `eq` function.
- If both are objects, they must have the same property names, and corresponding property values must be equal according to the `eq` function. The order in which the properties are written does not affect equality.
- Otherwise, they must be aliases referring to the same data in memory.

Ordering comparisons work on booleans, numbers, strings, and arrays, as follows:

- For booleans, `false` is less than `true`.
- Numbers are compared by numerical value.
- Strings are compared lexicographically by their Unicode code points.
- Arrays are compared lexicographically by their elements.

### eq|eq

Returns whether its arguments have equal values, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Any_): The first value.
- `b` (_Any_): The second value.

Returns:

- (_Boolean_): Whether the values are equal.

```
# Equality
[
    [eq(null, null)],
    [eq(true, true), eq(false, true), eq(false, false)],
    [eq(42, 42), eq(42, 43)],
    [eq("foo", "foo"), eq("foo", "bar")],
    [
        eq(["foo", "bar"], ["foo", "bar"]),
        eq(["foo", "bar"], ["foo", "baz"]),
    ],
    [
        eq([[1, 2], [3, 4]], [[1, 2], [3, 4]]),
        eq([[1, 2], [3, 4]], [[1, 2], [3, 5]]),
    ],
    [
        eq(
            {foo: "bar", spam: "eggs"},
            {spam: "eggs", foo: "bar"},
        ),
        eq(
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

### lt|lt

Returns whether its first argument is less than its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is less than `b`.

```
# Less than
[
    [
        false | lt(true),
        true | lt(true),
        true | lt(false),
    ],
    [
        42 | lt(43),
        43 | lt(43),
        43 | lt(42),
    ],
    [
        "bar" | lt("baz"),
        "baz" | lt("baz"),
        "baz" | lt("bar"),
    ],
    [
        ["foo", "bar"] | lt(["foo", "baz"]),
        ["foo", "bar"] | lt(["foo!", "aar"]),
        ["foo", "bar"] | lt(["foo", "bar"]),
        ["foo", "baz"] | lt(["foo", "bar"]),
        ["foo!", "aar"] | lt(["foo", "bar"]),
    ],
    [
        [[1, 2], [3, 4]] | lt([[1, 2], [3, 10]]),
        [[1, 2], [3, 4]] | lt([[1, 3], [3, 3]]),
        [[1, 2], [3, 4]] | lt([[1, 2], [3, 4]]),
        [[1, 2], [3, 10]] | lt([[1, 2], [3, 4]]),
        [[1, 3], [3, 3]] | lt([[1, 2], [3, 4]]),
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

### le|le

Returns whether its first argument is less than or equal to its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is less than or equal to `b`.

```
# Less than or equal
[
    42 | le(43),
    43 | le(43),
    43 | le(42),
]
>> [true, true, false]
```

### gt|gt

Returns whether its first argument is greater than its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is greater than `b`.

```
# Greater than
[
    42 | gt(43),
    43 | gt(43),
    43 | gt(42),
]
>> [false, false, true]
```

### ge|ge

Returns whether its first argument is greater than or equal to its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is greater than or equal to `b`.

```
# Greater than or equal
[
    42 | ge(43),
    43 | ge(43),
    43 | ge(42),
]
>> [false, true, true]
```

### isBetween|isBetween

Returns whether its first argument is between the specified lower and upper bounds, according to the [Comparison Rules](#comparison-rules).

Both bounds are inclusive.

Parameters:

- `n` (_Number, String, Boolean, or Array_): The value to test.
- `lower` (_Number, String, Boolean, or Array_): The lower bound.
- `upper` (_Number, String, Boolean, or Array_): The upper bound.

Returns:

- (_Boolean_): Whether `n` is between `lower` and `upper`.

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

### least|least

Returns the least element in the specified sequence, i.e. the element that is less than or equal to all other elements, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `sequence` (_Sequence_): The values to find the least element of.

Returns:

- (_Number, String, Boolean, or Array_): The least element.

```
# Least
[97, 42, 216] | least
>> 42
```

### greatest|greatest

Returns the greatest element in the specified sequence, i.e. the element that is greater than or equal to all other elements, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `sequence` (_Sequence_): The values to find the greatest element of.

Returns:

- (_Number, String, Boolean, or Array_): The greatest element.

```
# Greatest
[97, 42, 216] | greatest
>> 216
```

## Logic|logic

### and|and

Returns whether all given conditions evaluate to `true`. If any condition evaluates to `false`, the remaining conditions are not evaluated.

Parameters:

- `first` (_Boolean_): The first condition.
- `*rest` (_Array of Function returning Boolean_): The remaining conditions.

Returns:

- (_Boolean_): Whether all conditions are `true`.

```
# And
[
    and(true, $ true),
    and(true, $ false),
    and(false, $ true),
    and(false, $ false),
]
>> [true, false, false, false]
```

```
# And short-circuiting
[
    and(false, $ [] @ 1),
    and(false, $ (foo = foo; foo)),
]
>> [false, false]
```

### or|or

Returns whether at least one of the given conditions evaluates to `true`. If any condition evaluates to `true`, the remaining conditions are not evaluated.

Parameters:

- `first` (_Boolean_): The first condition.
- `*rest` (_Array of Function returning Boolean_): The remaining conditions.

Returns:

- (_Boolean_): Whether any condition is `true`.

```
# Or
[
    or(true, $ true),
    or(true, $ false),
    or(false, $ true),
    or(false, $ false),
]
>> [true, true, true, false]
```

```
# Or short-circuiting
[
    or(true, $ [] @ 1),
    or(true, $ (foo = foo; foo)),
]
>> [true, true]
```

### not|not

Returns the logical negation of its argument.

Parameters:

- `x` (_Boolean_): The value to negate.

Returns:

- (_Boolean_): The negated value.

```
# Not
[
    not(true),
    not(false),
]
>> [false, true]
```

## Types and Type Conversion|types

### Type Constants|type-constants

A class value is defined for each of the basic types.

```
# Class constants
[
    Null,
    Boolean,
    Number,
    String,
    Array,
    Stream,
    Object,
    Function,
    Error,
    Class,
    Protocol,
]
| transform(|.name)
| toArray
>> ["Null", "Boolean", "Number", "String", "Array", "Stream", "Object", "Function", "Error", "Class", "Protocol"]
```

There are also protocol constants for the following protocols:

- `Sequence` covers collections with a definite order: strings, arrays, and streams.
- `Instance` covers all values except null, booleans, numbers, strings, arrays, objects, and functions.
- `Type` covers values that represent Kenpali types: classes and protocols.
- `Any` covers all values.

```
# Protocol constants
[
    Sequence,
    Instance,
    Type,
    Any,
]
| transform(|.name)
| toArray
>> ["Sequence", "Instance", "Type", "Any"]
```

### classOf|classOf

Returns the Kenpali class of its argument.

Parameters:

- `value` (_Any_): The value whose class to get.

Returns:

- (_Class_): The Kenpali class.

```
# Class of
[
    classOf(null),
    classOf(false),
    classOf(true),
    classOf(42),
    classOf("foo"),
    classOf([1, 2, 3]),
    classOf(1 | to(3)),
    classOf({foo: 1, bar: 2}),
    classOf((x) => x),
    classOf(newError("badIdea")),
    classOf(Number),
    classOf(Sequence),
]
| transform(|.name)
| toArray
>> ["Null", "Boolean", "Boolean", "Number", "String", "Array", "Stream", "Object", "Function", "Error", "Class", "Protocol"]
```

### isNull|isNull

Returns whether its argument is `null`.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is `null`.

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
    isNull(classOf),
    isNull((x) => x),
    isNull(newError("badIdea")),
    isNull(Number),
    isNull(Sequence),
]
>> [true, false, false, false, false, false, false, false, false, false, false, false, false]
```

### isBoolean|isBoolean

Returns whether its argument is a boolean.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a boolean.

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
    isBoolean(classOf),
    isBoolean((x) => x),
    isBoolean(newError("badIdea")),
    isBoolean(Number),
    isBoolean(Sequence),
]
>> [false, true, true, false, false, false, false, false, false, false, false, false, false]
```

### isNumber|isNumber

Returns whether its argument is a number.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a number.

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
    isNumber(classOf),
    isNumber((x) => x),
    isNumber(newError("badIdea")),
    isNumber(Number),
    isNumber(Sequence),
]
>> [false, false, false, true, false, false, false, false, false, false, false, false, false]
```

### toNumber|toNumber

Returns the numeric value of its argument.

Parameters:

- `value` (_String or Number_): The value to convert.

Returns:

- (_Number_): The numeric value.

Throws:

- `notNumeric`: If `value` is a string that cannot be parsed as a number.

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

### isString|isString

Returns whether its argument is a string.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a string.

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
    isString(classOf),
    isString((x) => x),
    isString(newError("badIdea")),
    isString(Number),
    isString(Sequence),
]
>> [false, false, false, false, true, false, false, false, false, false, false, false, false]
```

### display|display

Returns a string representation of its argument, suitable for reporting the result of a computation. This is generally *not* suitable for user-friendly text, which should use formatting logic specific to the type being displayed.

Calling `display` on a stream reports already evaluated elements but never forces further evaluation.

Parameters:

- `value` (_Any_): The value to convert.

Returns:

- (_String_): The string value.

```
# Display
[
    display(null),
    display(false),
    display(true),
    display(42),
    display(-2.5),
    display("foo"),
    display([1, 2, 3]),
    display(1 | to(3)),
    display({foo: "bar", "spam!": "eggs"}),
    display(display),
    display(newError("badIdea", foo: "bar")),
    display(Number),
    display(Sequence),
]
>> [
    "null",
    "false",
    "true",
    "42",
    "-2.5",
    "\"foo\"",
    "[1, 2, 3]",
    "Stream [...]",
    "{foo: \"bar\", \"spam!\": \"eggs\"}",
    "Function {name: \"display\"}",
    "Error {type: \"badIdea\", details: {foo: \"bar\"}, calls: []}",
    "Class {name: \"Number\"}",
    "Protocol {name: \"Sequence\"}",
]
```

```
# Display on streams
stream = 1 | to(4);
[
    stream | display,
    (
        stream | keepFirst(3) | toArray;
        stream | display
    ),
    (
        stream | toArray;
        stream | display
    ),
]
>> [
    "Stream [...]",
    "Stream [1, 2, 3...]",
    "Stream [1, 2, 3, 4]",
]
```

```
# Display on natural functions
foo = $ (
    bar = $ 42;
    $ bar
);
[
    ($ 42) | display,
    foo | display,
    foo() | display,
    foo()() | display,
]
>> [
    "Function {name: \"$main/$anon1\"}",
    "Function {name: \"$main/foo\"}",
    "Function {name: \"$main/foo/$anon1\"}",
    "Function {name: \"$main/foo/bar\"}",
]
```

### isArray|isArray

Returns whether its argument is an array.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is an array.

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
    isArray(classOf),
    isArray((x) => x),
    isArray(newError("badIdea")),
    isArray(Number),
    isArray(Sequence),
]
>> [false, false, false, false, false, true, false, false, false, false, false, false, false]
```

### toArray|toArray

Collects the elements of the specified sequence into an array.

Parameters:

- `value` (_Sequence_): The value to convert.

Returns:

- (_Array_): The array of elements.

### isStream|isStream

Returns whether its argument is a stream.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a stream.

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
    isStream(classOf),
    isStream((x) => x),
    isStream(newError("badIdea")),
    isStream(Number),
    isStream(Sequence),
]
>> [false, false, false, false, false, false, true, false, false, false, false, false, false]
```

### toStream|toStream

Returns a stream that iterates over the elements of the specified sequence.

If the argument is already a stream, the same stream is returned, rather than a wrapper, i.e. `x | toStream | eq(x)` is true if `x` is a stream.

Parameters:

- `value` (_Sequence_): The value to convert.

Returns:

- (_Stream_): The stream of elements.

### isObject|isObject

Returns whether its argument is an object.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is an object.

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
    isObject(classOf),
    isObject((x) => x),
    isObject(newError("badIdea")),
    isObject(Number),
    isObject(Sequence),
]
>> [false, false, false, false, false, false, false, true, false, false, false, false, false]
```

### toObject|toObject

Converts the specified value into an object.

If the value is an array or stream, its elements are treated as key-value pairs. Any duplicate keys are assigned the value from the _last_ pair with that key in the sequence.

If the value is an instance, an object containing its class name and properties is returned.

Parameters:

- `value` (_Array or Instance_): The value to convert. If an array or stream, it must contain tuples like _[string, any]_.

Returns:

- (_Object_): An object constructed from `value`.

```
# Object from properties
properties = [["foo", "bar"], ["spam", "eggs"]];
properties | toObject
>> {foo: "bar", spam: "eggs"}
```

```
# Object from properties with duplicates
properties = [["foo", "bar"], ["spam", "eggs"], ["foo", "baz"]];
properties | toObject
>> {foo: "baz", spam: "eggs"}
```

```
# Object from instance
newError("badIdea", foo: "bar") | toObject
>> {"#class": "Error", type: "badIdea", details: {foo: "bar"}, calls: []}
```

### isFunction|isFunction

Returns whether its argument is a function.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a function.

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
    isFunction(classOf),
    isFunction((x) => x),
    isFunction(newError("badIdea")),
    isFunction(Number),
    isFunction(Sequence),
]
>> [false, false, false, false, false, false, false, false, true, true, false, false, false]
```

### toFunction|toFunction

Converts the specified value to a function:

- If the argument is already a function, the same function is returned.
- Otherwise, a constant function that returns the specified value is returned.

Parameters:

- `value` (_Any_): The value to convert.

Returns:

- (_Function_): The function.

```
# To function
[
    toFunction(42)(97),
    toFunction(display)(97),
    toFunction(| add(3))(97),
]
>> [42, "97", 100]
```

### isError|isError

Returns whether its argument is an error.

Note that merely calling this function doesn't _catch_ a thrown error: if `x` throws, `x | isError` propagates the error rather than returning `true`. The error must be explicitly caught first, i.e. `try($ x, onError: itself) | isError`.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is an error.

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
    isError(classOf),
    isError((x) => x),
    isError(newError("badIdea")),
    isError(Number),
    isError(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, true, false, false]
```

### isClass|isClass

Returns whether its argument is a class.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a class.

```
# Is class
[
    isClass(null),
    isClass(false),
    isClass(true),
    isClass(42),
    isClass("foo"),
    isClass([1, 2, 3]),
    isClass(1 | to(3)),
    isClass({foo: 1, bar: 2}),
    isClass(classOf),
    isClass((x) => x),
    isClass(newError("badIdea")),
    isClass(Number),
    isClass(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, true, false]
```

### isProtocol|isProtocol

Returns whether its argument is a protocol.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a protocol.

```
# Is protocol
[
    isProtocol(null),
    isProtocol(false),
    isProtocol(true),
    isProtocol(42),
    isProtocol("foo"),
    isProtocol([1, 2, 3]),
    isProtocol(1 | to(3)),
    isProtocol({foo: 1, bar: 2}),
    isProtocol(classOf),
    isProtocol((x) => x),
    isProtocol(newError("badIdea")),
    isProtocol(Number),
    isProtocol(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, false, true]
```

### isSequence|isSequence

Returns whether its argument is a sequence—a string, array, or stream.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a sequence.

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
    isSequence(classOf),
    isSequence((x) => x),
    isSequence(newError("badIdea")),
    isSequence(Number),
    isSequence(Sequence),
]
>> [false, false, false, false, true, true, true, false, false, false, false, false, false]
```

### isType|isType

Returns whether its argument is a type—a class or protocol.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a type.

```
# Is type
[
    isType(null),
    isType(false),
    isType(true),
    isType(42),
    isType("foo"),
    isType([1, 2, 3]),
    isType(1 | to(3)),
    isType({foo: 1, bar: 2}),
    isType(classOf),
    isType((x) => x),
    isType(newError("badIdea")),
    isType(Number),
    isType(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, true, true]
```

### isInstance|isInstance

Returns whether its argument is an instance—a value with an object-like structure belonging to a specific class. All types other than functions and the JSON types are considered instances.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is an instance.

```
# Is instance
[
    isInstance(null),
    isInstance(false),
    isInstance(true),
    isInstance(42),
    isInstance("foo"),
    isInstance([1, 2, 3]),
    isInstance(1 | to(3)),
    isInstance({foo: 1, bar: 2}),
    isInstance(classOf),
    isInstance((x) => x),
    isInstance(newError("badIdea")),
    isInstance(Number),
    isInstance(Sequence),
]
>> [false, false, false, false, false, false, true, false, false, false, true, true, true]
```

## Control Flow|control-flow

### if|if

Evaluates and returns the result of one of two functions based on a condition.

Parameters:

- `condition` (_Boolean_): The condition to check.
- `then:` (_Function_): The function to call if `condition` is `true`.
- `else:` (_Function_): The function to call if `condition` is `false`.

Returns:

- (_Any_): The result of the evaluated function.

```
# If
[
    if(true, then: $ 1, else: $ 2),
    if(false, then: $ 1, else: $ 2),
]
>> [1, 2]
```

```
# If short-circuiting
[
    if(true, then: $ 1, else: $ [] @ 1),
    if(true, then: $ 1, else: $ (foo = foo; foo)),
    if(false, then: $ [] @ 1, else: $ 2),
    if(false, then: $ (foo = foo; foo), else: $ 2),
]
>> [1, 1, 2, 2]
```

### butIf|butIf

Substitutes a different value if a condition is true.

Parameters:

- `value` (_Any_): The original value.
- `condition` (_Boolean or Function_): The condition to check. If this is a function, it is called with `value` as its argument to get the condition's truth value.
- `ifTrue` (_Function_): A function that returns the value to substitue if `condition` is true. This is called with `value` as its argument.

Returns:

- (_Any_): `value` if `condition` is false, the result of `ifTrue` otherwise.

```
# But if
[
    42 | butIf(| gt(10), | sub(5)),
    7 | butIf(| gt(10), | sub(5)),
]
>> [37, 7]
```

### ifs|ifs

Evaluates a list of conditions and returns the result of the first matching case.


Parameters:

- `conditions` (_Array of tuple like [Function, Function]_): A list of condition-result pairs. Each condition function is called with no arguments, and if it returns `true`, the corresponding result function is called with no arguments and its result is returned.
- `else:` (_Function_): The function to call if no conditions match.

Returns:

- (_Any_): The result of the first matching case, or the `else` function if no conditions match.

```
# Multi-way if
foo = (a, b) => ifs(
    [$ a | lt(b), $ "Too small!"],
    [$ a | gt(b), $ "Too big!"],
    else: $ "Just right!",
);
[
    foo(97, 42),
    foo(1, 42),
    foo(42, 42),
]
>> ["Too big!", "Too small!", "Just right!"]
```

### switch|switch

Evaluates a list of conditions on an input value and returns the result of the first matching case.

Parameters:

- `value` (_Any_): The value to pass to conditions and results.
- `conditions` (_Array of tuple like [Function, Function]_): A list of condition-result pairs. Each condition function is called with `value`, and if it returns `true`, the corresponding result function is called with `value` and its result is returned.
- `else:` (_Function_): The function to call if no conditions match.

Returns:

- (_Any_): The result of the first matching case, or the `else` function if no conditions match.

```
# Switch
withComment = (value, comment) => [value | display, comment] | join;
foo = | switch(
    [| lt(42), | withComment(" is too small!")],
    [| gt(42), | withComment(" is too big!")],
    else: | withComment(" is just right!"),
);
[
    foo(97),
    foo(1),
    foo(42),
]
>> ["97 is too big!", "1 is too small!", "42 is just right!"]
```

## Stream Builders|stream-builders

These functions build up new streams from scalar inputs.

### Stream|stream

#### newStream|newStream

Explicitly creates a stream with the specified head value and tail stream.

Parameters:

- `value:` (_Function_): A function that returns the first value in the stream.
- `next:` (_Function_): A function that returns the remaining values in the stream, as a stream. This normally makes a recursive call to the containing function, allowing the stream to continue indefinitely.

Returns:

- (_Stream_): The new stream.

```
# Explicitly creating a stream
myStream = (start) => newStream(
    value: $ start,
    next: $ myStream(start | mul(2))
);
1 | myStream | keepFirst(5) | toArray
>> [1, 2, 4, 8, 16]
```

#### emptyStream|emptyStream

Creates a stream with no elements. Together with `newStream`, this allows explicitly creating finite streams.

Returns:

- (_Stream_): An empty stream.

```
# Empty stream
emptyStream() | toArray
>> []
```

```
# Explicitly creating a finite stream
myStream = (start) => if(
    start | lt(100),
    then: $ newStream(
        value: $ start,
        next: $ myStream(start | mul(2))
    ),
    else: $ emptyStream(),
);
1 | myStream | toArray
>> [1, 2, 4, 8, 16, 32, 64]
```

#### Stream/isEmpty|stream-isEmpty

Returns whether the specified stream is empty.

Returns:

- (_Boolean_): True if the stream is empty.

```
# Stream is empty
[
    emptyStream().isEmpty(),
    1 | to(3) |.isEmpty(),
]
>> [true, false]
```

#### Stream/value|stream-value

Returns the first element of the specified stream.

This property is _absent_ if the stream is empty.

Returns:

- (_Any_): The first element.

```
# Stream value
[
    try($ emptyStream().value(), onError: itself).type,
    1 | to(3) |.value(),
]
>> ["missingProperty", 1]
```

#### Stream/next|stream-next

Returns the rest of the specified stream.

This property is _absent_ if the stream is empty.

Returns:

- (_Stream_): The rest of the stream.

```
# Stream next
[
    try($ emptyStream().next(), onError: itself).type,
    1 | to(3) |.next() | toArray,
]
>> ["missingProperty", [2, 3]]
```

### build|build

Generates a stream by repeatedly applying a function to a start value.

Parameters:

- `start` (_Any_): The start value.
- `next` (_Function_): A function that computes the next state from the current state.

Returns:

- (_Stream_): A stream containing `start` and all values produced by the `next` function.

```
# Build
powersOfTwo = 1 | build(| mul(2));
[
    powersOfTwo | isStream,
    powersOfTwo @ 1,
    powersOfTwo @ 3,
    powersOfTwo @ 8,
]
>> [true, 1, 4, 128]
```

```
# Build only invokes the function when needed
1
| build((x) => if(
    x | ge(3),
    then: $ throw(newError("tooBig")),
    else: $ x | up
))
| keepFirst(3)
| toArray
>> [1, 2, 3]
```

```
# Build only invokes the function once for each iteration
out = newMutableArray();
1 | build((x) => (out.append(x); x | up))
| keepFirst(4)
| toArray;
out.elements()
>> [1, 2, 3]
```

```
# Build doesn't overflow the stack
1 | build(up) @ 9999
>> 9999
```

### to|to

Generates a stream of numbers covering a range.

This is often useful for situations where other languages would use `for` loops.

Parameters:

- `start` (_Number_): The number to start counting from. This is always the first element of the stream.
- `end` (_Number_): The number to stop at or before.
- `by:` (_Number_, default `1`): The number to count by. Each element of the stream will be `by` more than the previous one. If `by` is negative, the stream will count down. If the sign of `by` is opposite the sign of `end - start` (i.e. counting the "wrong way"), the resulting stream is empty.

Returns:

- (_Stream_): A stream of numbers ranging from `start` to `end`, incrementing by `by`.

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

### toSize|toSize

Generates a stream of incrementing numbers with a given size.

Parameters:

- `start` (_Number_): The number to start counting from.
- `size` (_Number_): The number of elements to include in the stream.

Returns:

- (_Stream_): A stream of numbers counting up from `start`, with `size` elements in total.

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

### repeat|repeat

Creates a stream that repeats the same value forever.

Parameters:

- `value` (_Any_): The value to repeat.

Returns:

- (_Stream_): An infinite stream all of whose elements are `value`.

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

## Stream Collapsers|stream-collapsers

These functions exhaust an input stream to produce a scalar output or side effect. They loop forever if given an infinite stream.

### last|last

Returns the last element of the specified sequence. Equivalent to indexing with `-1`.

Parameters:

- `sequence` (_Sequence_): The sequence to get the last element of.

Returns:

- (_Any_): The last element.

```
# Last element
[
    ["foo"] | last,
    ["foo", "bar", "baz"] | last,
    1 | to(5) | last,
]
>> ["foo", "baz", 5]
```

### length|length

Returns the number of elements in the specified sequence.

For strings, this is the number of characters.

Parameters:

- `sequence` (_Sequence_): The sequence to find the length of.

Returns:

- (_Number_): The length.

```
# Sequence length
[
    length(""),
    length("f"),
    length("foo"),
    length([]),
    length(["foo"]),
    length(["foo", "bar", "baz"]),
    length(1 | to(0)),
    length(1 | to(5)),
]
>> [0, 1, 3, 0, 1, 3, 0, 5]
```

### keepLast|keepLast

Retains only the last `n` elements of the input.

Parameters:

- `sequence` (_Sequence_): The sequence to take values from.
- `n` (_Number_): The number of elements to keep.

Returns:

- (_Array or String_): An array of at most `n` elements, or a string containing the last `n` characters if `sequence` is a string.

```
# Keeping trailing elements
[
    "foobar" | keepLast(3),
    [42, 97, 6, 12, 64] | keepLast(3),
    1 | to(5) | keepLast(3),
]
>> [
    "bar",
    [6, 12, 64],
    [3, 4, 5],
]
```

### dropLast|dropLast

Drops the last `n` elements of the input.

Parameters:

- `sequence` (_Sequence_): The sequence to drop values from.
- `n` (_Number_, default: `1`): The number of elements to drop.

Returns:

- (_Array or String_): An array with the last `n` elements dropped, or a string with the last `n` characters dropped if `sequence` is a string.

```
# Dropping trailing elements
[
    "foobar" | dropLast,
    "foobar" | dropLast(2),
    [42, 97, 6, 12, 64] | dropLast,
    [42, 97, 6, 12, 64] | dropLast(3),
    1 | to(5) | dropLast,
    1 | to(5) | dropLast(3),
]
>> [
    "fooba",
    "foob",
    [42, 97, 6, 12],
    [42, 97],
    [1, 2, 3, 4],
    [1, 2],
]
```

### count|count

Returns the number of elements in the sequence matching the specified condition.

Parameters:

- `sequence` (_Sequence_): The sequence to count from.
- `condition` (_Function_): A function that returns `true` for elements to count.

Returns:

- (_Number_): The number of elements for which `condition` returns `true`.

```
# Counting
[
    [1, 10, 2, 9, 3, 12] | count(| lt(10)),
    [1, 10, 2, 9, 3, 12] | toStream | count(| lt(10)),
]
>> [4, 4]
```

### forAll|forAll

Tests whether the specified condition is true for all elements in the sequence.

Parameters:

- `sequence` (_Sequence_): The sequence to test.
- `condition` (_Function_): A function that returns `true` for acceptable elements.

Returns:

- (_Boolean_): Whether all elements match the condition.

```
# True for all elements
[
    [1, 2, 3] | forAll(| lt(10)),
    [1, 42, 3] | forAll(| lt(10)),
    [1, 2, 3] | toStream | forAll(| lt(10)),
    [1, 42, 3] | toStream | forAll(| lt(10)),
]
>> [true, false, true, false]
```

### forSome|forSome

Tests whether the specified condition is true for at least one element in the sequence.

Parameters:

- `sequence` (_Sequence_): The sequence to test.
- `condition` (_Function_): A function that returns `true` for acceptable elements.

Returns:

- (_Boolean_): Whether any elements match the condition.

```
# True for some elements
[
    [41, 2, 43] | forSome(| lt(10)),
    [41, 42, 43] | forSome(| lt(10)),
    [41, 2, 43] | toStream | forSome(| lt(10)),
    [41, 42, 43] | toStream | forSome(| lt(10)),
]
>> [true, false, true, false]
```

### reverse|reverse

Returns an array containing all the elements of the specified sequence in reverse order.

Parameters:

- `sequence` (_Sequence_): The sequence to reverse.

Returns:

- (_Array_): The reversed array.

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

### sort|sort

Returns an array with the same elements as the specified sequence, but in ascending order.

If `by` is `null`, the elements are sorted in their natural order, following the [Comparison Rules](#comparison-rules).

Otherwise, `by` is called on each element to obtain a sort key, and the elements are sorted by that sort key, again following the Comparison Rules. The sort is stable: two elements with equal sort keys will appear in the same relative order in the sorted array as they were in the original sequence.

Parameters:

- `sequence` (_Sequence_): The sequence to sort.
- `by:` (_Function or Null_, default `null`): A function to produce a sort key from each element, or `null` to sort the elements in their natural order.

Returns:

- (_Array_): The sorted array.

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

### group|group

Groups a sequence of pairs by their first element.

Parameters:

- `pairs` (_Sequence_): A sequence of pairs whose first element is the grouping key.
- `onGroup:` (_Function_, default `(x) => x`): A function to call on each group after assembling it.

Returns:

- (_Array_): A sequence of pairs whose first element is the grouping key, and whose second element is the result of calling `onGroup` on an array of the second elements of the input pairs with that grouping key.

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

### groupBy|groupBy

Groups a sequence by a key function.

Parameters:

- `sequence` (_Sequence_): The sequence to group.
- `by` (_Function_): A function that returns a grouping key for a given value.
- `onGroup:` (_Function_, default `(x) => x`): A function to call on each group after assembling it.

Returns:

- (_Array_): A sequence of pairs whose first element is the grouping key, and whose second element is the result of calling `onGroup` on an array of the elements with that grouping key.

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

### forEach|forEach

Applies a function to each element in a sequence for its side effects.

Parameters:

- `sequence` (_Sequence_): The sequence of values to iterate over.
- `action` (_Function_): The function to apply to each element.

Returns:

- (_Array_): The input sequence as an array.

```
# Applying a side effect to each element
result = newMutableArray();
["foo", "bar", "baz"] | forEach(result.append);
1 | to(5) | forEach(result.append);
result.elements()
>> ["foo", "bar", "baz", 1, 2, 3, 4, 5]
```

## Stream Accessors|stream-accessors

These functions calculate a scalar value from a stream, but only access a finite number of elements to do so. Therefore, they are safe to call even on infinite streams.

### isEmpty|isEmpty

Tests whether the specified sequence has no elements.

Parameters:

- `sequence` (_Sequence_): The sequence to check for emptiness.

Returns:

- (_Boolean_): Whether the sequence is empty.

```
# Is empty
[
    "" | isEmpty,
    "foo" | isEmpty,
    [] | isEmpty,
    [1] | isEmpty,
    [] | toStream | isEmpty,
    [1] | toStream | isEmpty,
    1 | repeat | isEmpty,
    newStream(value: $ throw(newError("badIdea")), next: emptyStream) | isEmpty,
]
>> [
    true,
    false,
    true,
    false,
    true,
    false,
    false,
    false,
]
```

### first|first

Returns the first element of the specified sequence. Equivalent to indexing with `1`.

Parameters:

- `sequence` (_Sequence_): The sequence to get the first element of.

Returns:

- (_Any_): The first element.

```
# First element
[
    ["foo"] | first,
    ["foo", "bar", "baz"] | first,
    1 | to(5) | first,
    1 | build(| mul(2)) | first,
    1 | build($ throw(newError("badIdea"))) | first,
]
>> ["foo", "foo", 1, 1, 1]
```

## Stream Rebuilders|stream-rebuilders

These functions create new streams that depend on existing ones, preserving stream laziness.

### transform|transform

Creates a stream from applying a function to each element in an input sequence.

Parameters:

- `sequence` (_Sequence_): The sequence of values to transform.
- `f` (_Function_): The function to apply to each element.

Returns:

- (_Stream_): A stream of transformed values.

```
# Transforming
[
    [1, 2, 3] | transform((i) => mul(i, i)) | toArray,
    1 | to(3) | transform((i) => mul(i, i)) | toArray,
    1 | build(| up)
    | transform((i) => mul(i, i))
    | keepFirst(3)
    | toArray,
    1
    | build($ throw(newError("badIdea")))
    | transform((i) => mul(i, i))
    | first,
]
>> [
    [1, 4, 9],
    [1, 4, 9],
    [1, 4, 9],
    1,
]
```

### running|running

Maintains state while processing a sequence, producing a stream of intermediate states.

Parameters:

- `sequence` (_Sequence_): The sequence of values to process.
- `start:` (_Any_): The initial state.
- `next:` (_Function_): A function that computes the next state from the current element and state. The state is passed as a named argument `state:`.

Returns:

- (_Stream_): A stream containing `start` and all values produced by the `next` function.

```
# Running state
[2, 8, 9, 3, 7]
| running(
    start: 0,
    next: (number, state: total) => (
        total | mul(10) | add(number)
    ),
)
| toArray
>> [0, 2, 28, 289, 2893, 28937]
```

```
# Running only invokes `next` when needed
[2, 8, 9, 3, 7]
| running(
    start: 0,
    next: $ throw(newError("badIdea"))
)
| first
>> 0
```

```
# Running doesn't overflow the stack
repeat(1) | running(start: 1, next: (number, state:) => state | add(number)) @ 9999
>> 9999
```

### withIndex|withIndex

Tags each element of the input sequence with its index.

Parameters:

- `sequence` (_Sequence_): The sequence to add indices to.

Returns:

- (_Stream_): A stream of pairs, where the first element is the index and the second element is the element from the input sequence.

```
# Adding indices
["foo", "bar", "baz"] | withIndex | toArray
>> [[1, "foo"], [2, "bar"], [3, "baz"]]
```

```
# Adding indices only advances the input stream when needed
1 | build($ throw(newError("badIdea"))) | withIndex | first
>> [1, 1]
```

### keepFirst|keepFirst

Retains only the first `n` elements of the input.

Parameters:

- `sequence` (_Sequence_): The sequence to take values from.
- `n` (_Number_): The number of elements to keep.

Returns:

- (_Stream or String_): A stream of at most `n` elements, or a string containing the first `n` characters if `sequence` is a string.

```
# Keeping leading elements
[
    "foobar" | keepFirst(3),
    [42, 97, 6, 12, 64] | keepFirst(3) | toArray,
    1 | build(| mul(2)) | keepFirst(3) | toArray,
    1 | build($ throw(newError("badIdea"))) | keepFirst(1) | toArray,
]
>> [
    "foo",
    [42, 97, 6],
    [1, 2, 4],
    [1],
]
```

### dropFirst|dropFirst

Skips the first `n` elements of the input.

Parameters:

- `sequence` (_Sequence_): The sequence to skip values from.
- `n` (_Number_, default: `1`): The number of elements to drop.

Returns:

- (_Stream or String_): A stream with the first `n` elements skipped, or a string with the first `n` characters skipped if `sequence` is a string.

```
# Dropping leading elements
[
    "foobar" | dropFirst,
    "foobar" | dropFirst(2),
    [42, 97, 6, 12, 64] | dropFirst | toArray,
    [42, 97, 6, 12, 64] | dropFirst(3) | toArray,
    1 | build(| mul(2)) | dropFirst(3) | keepFirst(3) | toArray,
    [[42, 97, 6], 1 | build($ throw(newError("badIdea")))]
    | flatten
    | dropFirst(1)
    | keepFirst(3)
    | toArray,
]
>> [
    "oobar",
    "obar",
    [97, 6, 12, 64],
    [12, 64],
    [8, 16, 32],
    [97, 6, 1],
]
```

```
# Dropping doesn't ask for any of the dropped values
fs = [$ throw(newError("badIdea")), $ throw(newError("stillBadIdea")), $ 42, $ 97];
1 | to(4) | transform((i) => (fs @ i)()) | dropFirst(2) | toArray
>> [42, 97]
```

### slice|slice

Extracts a sub-sequence of the specified sequence.

Parameters:

- `sequence` (_Sequence_): The sequence to take values from.
- `from:` (_Number_): The index of the first element to take.
- `to:` (_Number_): The index of the last element to take.

Returns:

- (_Stream or String_): A stream containing only the elements from index `from` to index `to`, or a string with the characters at those indices if `sequence` is a string.

```
# Slicing
[
    "foobar" | slice(from: 2, to: 4),
    "foobar" | slice(from: 2, to: 10),
    "foobar" | slice(from: 0, to: 4),
    [42, 97, 6, 12, 64] | slice(from: 2, to: 4) | toArray,
    [42, 97, 6, 12, 64] | slice(from: 2, to: 10) | toArray,
    [42, 97, 6, 12, 64] | slice(from: 0, to: 4) | toArray,
    1 | build(| mul(2)) | slice(from: 2, to: 4) | toArray,
    [[42, 97, 6], 1 | build($ throw(newError("badIdea")))]
    | flatten
    | slice(from: 2, to: 4)
    | toArray,
]
>> [
    "oob",
    "oobar",
    "foob",
    [97, 6, 12],
    [97, 6, 12, 64],
    [42, 97, 6, 12],
    [2, 4, 8],
    [97, 6, 1],
]
```

### while|while

Creates a stream of elements from the input sequence while a condition holds.

Parameters:

- `sequence` (_Sequence_): The sequence to process.
- `condition` (_Function returning Boolean_): A function that returns `false` when the stream should stop.

Returns:

- (_Stream_): A stream containing elements for which `condition` returns `true`, stopping at the first `false`.

```
# While
1
| build(| mul(2))
| while(| lt(100))
| toArray
>> [1, 2, 4, 8, 16, 32, 64]
```

```
# While doesn't ask for values beyond the stopping condition
1 | build($ throw(newError("badIdea"))) | while(| lt(0)) | toArray
>> []
```

### continueIf|continueIf

Creates a stream of elements from the input sequence, continuing to the next element if a condition holds. The resulting stream has one extra element compared to `while`—the first element that doesn't satisfy the condition—which makes some stopping conditions easier to express.

Parameters:

- `sequence` (_Sequence_): The sequence to process.
- `condition` (_Function returning Boolean_): A function that returns `false` when the stream should stop.

Returns:

- (_Stream_): A stream containing the first element for which the condition returns `false`, and all elements before it.

```
# Continue-If
1
| build(| mul(2))
| continueIf(| lt(100))
| toArray
>> [1, 2, 4, 8, 16, 32, 64, 128]
```

```
# Continue-If doesn't ask for values beyond the stopping condition
1 | build($ throw(newError("badIdea"))) | continueIf(| lt(0)) | toArray
>> [1]
```

### thenRepeat|thenRepeat

Adds endless copies of a constant value to the end of the sequence.

This is useful if subsequent steps need to continue past the end of the stream.

Parameters:

- `sequence` (_Sequence_): The initial sequence.
- `value` (_Any_): The value to repeat after `sequence` is exhausted.

```
# Then repeat
[2, 8, 9, 3, 7] | thenRepeat(null) | keepFirst(8) | toArray
>> [2, 8, 9, 3, 7, null, null, null]
```

### sliding|sliding

Returns a stream containing arrays of adjacent elements in the input sequence.

Parameters:

- `sequence` (_Sequence_): The input sequence.
- `size` (_Number_): The number of adjacent elements in each output array.

Returns:

- (_Stream_): A stream containing arrays with `size` consecutive elements each. The first array starts with the first element of the input sequence, the second array with the second element, and so on, with the last array ending on the last element of the input sequence.

```
# Sliding window
[2, 8, 9, 3, 7] | sliding(3) | toArray
>> [[2, 8, 9], [8, 9, 3], [9, 3, 7]]
```

```
# Operating on a sliding window
diffs = (sequence) => (
    sequence
    | sliding(2)
    | transform(([a, b]) => b | sub(a))
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
# Sliding doesn't ask for values beyond its last window
[[2, 8, 9, 3], 1 | build($ throw(newError("badIdea")))]
| flatten
| sliding(3)
| keepFirst(3)
| toArray
>> [[2, 8, 9], [8, 9, 3], [9, 3, 1]]
```

### where|where

Filters a sequence, keeping only elements that satisfy a condition.

Parameters:

- `sequence` (_Sequence_): The sequence to filter.
- `condition` (_Function_): A function that returns `true` for elements to keep.

Returns:

- (_Stream_): A stream of elements where `condition` returns `true`.

```
# Filtering
[
    [1, 10, 2, 9, 3, 12] | where(| lt(10)) | toArray,
    [1, 10, 2, 9, 3, 12]
    | repeat
    | flatten
    | where(| lt(10))
    | keepFirst(5)
    | toArray,
]
>> [
    [1, 2, 9, 3],
    [1, 2, 9, 3, 1],
]
```

```
# Filtering doesn't ask for values beyond the last element it needs
[[1, 10, 2, 9, 3, 12], 1 | build($ throw(newError("badIdea")))]
| flatten
| where(| lt(10))
| keepFirst(5)
| toArray
>> [1, 2, 9, 3, 1]
```

### zip|zip

Combines multiple sequences into a stream of tuples, stopping when the shortest sequence is exhausted.

Parameters:

- `*sequences` (_Array of Sequence_): The sequences to combine.

Returns:

- (_Stream_): A stream of tuples, where each tuple contains corresponding elements from the input sequences.


```
# Zipping
[
    [1, 2, 3] | zip(["one", "two", "three"]) | toArray,
    [1, 2, 3] | zip(["one", "two"]) | toArray,
    1 | build(| mul(2)) | zip(["one", "two", "three"]) | toArray,
    1 | build(| mul(2)) | zip(1 | build(| mul(3))) | keepFirst(3) | toArray,
    1 | build($ throw(newError("badIdea"))) | zip(["foo"]) | toArray,
]
>> [
    [[1, "one"], [2, "two"], [3, "three"]],
    [[1, "one"], [2, "two"]],
    [[1, "one"], [2, "two"], [4, "three"]],
    [[1, 1], [2, 3], [4, 9]],
    [[1, "foo"]],
]
```

### unzip|unzip

Splits a sequence of tuples into multiple streams.

Parameters:

- `sequence` (_Sequence of Array_): A sequence where each element is a tuple.
- `numStreams:` (_Number_, default: `2`): The number of streams to produce.

Returns:

- (_Array of Stream_): An array of `numStreams` streams, where the `i`-th stream contains the `i`-th element from each tuple.

```
# Unzipping
[
    [[1, "one"], [2, "two"], [3, "three"]]
    | unzip
    | transform(| toArray)
    | toArray,
    [2, 2]
    | build(([a, b]) => [b | mul(2), a | up])
    | unzip
    | transform(| keepFirst(4) | toArray)
    | toArray,
    [1, "one"] | build($ throw(newError("badIdea")))
    | unzip
    | transform(first)
    | toArray,
]
>> [
    [[1, 2, 3], ["one", "two", "three"]],
    [[2, 4, 6, 10], [2, 3, 5, 7]],
    [1, "one"]
]
```

```
# Unzipping with more than two streams
[
    [[1, "one", "eins"], [2, "two", "zwei"], [3, "three", "drei"]]
    | unzip(numStreams: 3)
    | transform(| keepFirst(3) | toArray)
    | toArray,
]
>> [
    [[1, 2, 3], ["one", "two", "three"], ["eins", "zwei", "drei"]],
]
```

### flatten|flatten

Flattens a sequence of sequences into a single stream.

Parameters:

- `sequences` (_Sequence of Sequence_): The sequence to flatten.

Returns:

- (_Stream_): A stream containing all elements from the nested sequences.

```
# Flattening nested sequences
[
    [] | flatten | toArray,
    [[]] | flatten | toArray,
    [[1], [2, 3], [4, 5, [6]]] | flatten | toArray,
    [[1], [2, 3], []] | flatten | toArray,
    [1] | build((a) => [*a, a | last | up]) | flatten | keepFirst(7) | toArray,
    [[1], 1 | build($ throw(newError("badIdea")))] | flatten | keepFirst(2) | toArray,
    [[1], newStream(value: $ throw(newError("badIdea")), next: emptyStream)] | flatten | keepFirst(1) | toArray,
    [1] | build($ throw(newError("badIdea"))) | flatten | first,
]
>> [
    [],
    [],
    [1, 2, 3, 4, 5, [6]],
    [1, 2, 3],
    [1, 1, 2, 1, 2, 3, 1],
    [1, 1],
    [1],
    1,
]
```

### dissect|dissect

Splits a sequence into chunks at elements that satisfy a condition.

Parameters:

- `sequence` (_Sequence_): The sequence to process.
- `condition` (_Function_): A function that determines split points.

Returns:

- (_Stream of Array_): A stream of arrays, where each array contains a group of elements up to and including a split point.

```
# Dissecting
[
    [] | dissect(| ge(8)) | toArray,
    [2, 8, 9, 3, 7] | dissect(| ge(8)) | toArray,
    [2, 8, 9, 3, 7] | repeat | flatten | dissect(| ge(8)) | keepFirst(5) | toArray,
    9 | build($ throw(newError("badIdea"))) | dissect(| ge(8)) | first,
]
>> [
    [],
    [[2, 8], [9], [3, 7]],
    [[2, 8], [9], [3, 7, 2, 8], [9], [3, 7, 2, 8]],
    [9],
]
```

### chunk|chunk

Splits a sequence into arrays of a fixed size.

Parameters:

- `sequence` (_Sequence_): The sequence to split.
- `size` (_Number_): The number of elements in each output array.

Returns:

- (_Stream_): A stream containing arrays with `size` elements each, except that the last array may have fewer elements.

```
# Chunking into arrays of a fixed size
[
    1 | to(9) | chunk(3) | toArray,
    1 | to(10) | chunk(3) | toArray,
    1 | to(11) | chunk(3) | toArray,
    1 | build(| mul(2)) | chunk(3) | keepFirst(3) | toArray,
    [1 | to(9), newStream(value: $ throw(newError("badIdea")), next: emptyStream)]
    | flatten
    | chunk(3)
    | keepFirst(3)
    | toArray,
]
>> [
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11]],
    [[1, 2, 4], [8, 16, 32], [64, 128, 256]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
]
```

## Objects|objects

### keys|keys

Returns an array of the keys in an object.

Parameters:

- `object` (_Object_): The object whose keys to retrieve.

Returns:

- (_Array_): An array of the object's keys.

```
# Object keys
object = {foo: "bar", spam: "eggs"};
object | keys
>> ["foo", "spam"]
```

### properties|properties

Returns an array of the key-value pairs in the object.

Parameters:

- `object` (_Object_): The object whose properties to retrieve.

Returns:

- (_Array_): An array of the object's properties.

```
# Array of properties
{foo: 1, bar: 2} | properties
>> [["foo", 1], ["bar", 2]]
```

### merge|merge

Combines the properties of the specified objects into a new object.

If the same key appears in more than one input object, the resulting object takes the property value from the last such object.

Parameters:

- `objects` (_Array of Object_): The objects to combine.

Returns:

- (_Object_): The combined object.

```
# Merging
[{foo: 1, bar: 2}, {bar: 3, baz: 4}] | merge
>> {foo: 1, bar: 3, baz: 4}
```

## Indexing|indexing

### at|at

Returns the value at the specified index in the specified collection, or a default value if the index is invalid.

Without the default value, `a | at(b)` is equivalent to `a @ b`.

Parameters:

- `collection` (_Sequence or Object_): The collection to index into.
- `index` (_Number or String_): The index value.
- `default:` (_Function or Null_, default `null`): A function to call and return the result of if the index is invalid, or `null` to throw an error if the index is invalid.

```
# Indexing arrays
[
    ["foo", "bar", "baz"] | at(1),
    ["foo", "bar", "baz"] | at(2),
    ["foo", "bar", "baz"] | at(3),
    ["foo", "bar", "baz"] | at(2, default: $ "other"),
    ["foo", "bar", "baz"] | at(-2),
    ["foo", "bar", "baz"] | at(-2, default: $ "other"),
    ["foo", "bar", "baz"] | at(4, default: $ "other"),
    ["foo", "bar", "baz"] | at(0, default: $ "other"),
    ["foo", "bar", "baz"] | at(-4, default: $ "other"),
]
>> ["foo", "bar", "baz", "bar", "bar", "bar", "other", "other", "other"]
```

Finite streams can be indexed as if they were arrays.

```
# Indexing finite streams
[
    2 | to(5) | at(1),
    2 | to(5) | at(2),
    2 | to(5) | at(4),
    2 | to(5) | at(2, default: $ 42),
    2 | to(5) | at(-2),
    2 | to(5) | at(-2, default: $ 42),
    2 | to(5) | at(5, default: $ 42),
    2 | to(5) | at(0, default: $ 42),
    2 | to(5) | at(-5, default: $ 42),
    1 | build($ throw(newError("badIdea"))) | at(1),
]
>> [2, 3, 5, 3, 4, 4, 42, 42, 42, 1]
```

Infinite streams only accept positive indices. They loop forever if the index is negative.

```
# Indexing infinite streams
[
    2 | build(| mul(2)) | at(2),
    2 | build(| mul(2)) | at(2, default: $ 42),
    2 | build(| mul(2)) | at(0, default: $ 42),
]
>> [4, 4, 42]
```

```
# Indexing objects
object = {foo: "bar", spam: "eggs"};
[
    object | at("foo"),
    object | at("foo", default: $ "nothing"),
    object | at("baz", default: $ "nothing"),
]
>> ["bar", "bar", "nothing"]

```

## Utilities|utilities

### debug|debug

Writes the specified value to a diagnostic log. Exactly where this goes depends on the implementation and configuration; writing to standard error is a common default.

This function returns its argument, allowing it to be dropped into pipelines temporarily without disrupting their structure. For example, `42 | foo | bar | debug | baz` has the same result as `42 | foo | bar | baz`, but it writes `bar`'s return value to the diagnostic log.

Parameters:

- `value` (_Any_): The value to write.
- `name` (_String or Null_, default `null`): If provided, the output value will be tagged with this, making it clearer which debug values came from where.

Returns:

- (_Any_): The `value` argument.

```
# Debug
1 | build(| mul(2) | debug) @ 10 // Open the console to see the debug output!
>> 512
```

### itself|itself

Returns its argument. This is useful when a function must be provided, but no transformation is needed, and can be clearer than just writing `(x) => x`.

Parameters:

- `x` (_Any_): The argument.

Returns:

- (_Any_): The argument.

```
# Identity function
[
    42 | itself,
    "foo" | itself,
    [{foo: 1, bar: 2}, {bar: 3, baz: 4}] | itself,
]
>> [
    42,
    "foo",
    [{foo: 1, bar: 2}, {bar: 3, baz: 4}],
]
```

### callOnce|callOnce

Creates a function that evaluates its body only the first time it is called, and saves the result for subsequent calls.

Parameters:

- `body` (_Function_): A zero-argument function to evaluate on the first call.

Returns:

- (_Function_): The wrapped function.

```
# Ensuring a function is called only once
out = newMutableArray();
foo = callOnce($ 42 | also(| out.append));
[foo(), foo(), foo(), out.elements()]
>> [42, 42, 42, [42]]
```

## Sets and Maps|sets-maps

### Set|Set

A `Set` is like an array, but it can't have duplicate elements, and checking whether an element is in the set is fast regardless of the size of the set.

#### newSet|newSet

Creates an immutable set.

Parameters:

- `elements` (_Array_, default `[]`): The set's elements. Duplicate elements are silently discarded.

Returns:

- (_Set_): The new set.

#### Set/size|Set-size

Returns:

- (_Number_): The number of elements in the set.

#### Set/elements|Set-elements

Returns:

- (_Array_): An array containing all the elements in the set.

#### Set/has|Set-has

Checks whether the specified element is in the set.

Parameters:

- `element` (_Any_): The value to look for.

Returns:

- (_Boolean_): Whether the value is in the set.

```
# Set
set = ["foo", "bar", "baz"] | newSet;
[
    set.size(),
    set.elements(),
    set.has("foo"),
    set.has("baz"),
    set.has("spam"),
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
    set.has(["foo"]),
    set.has({foo: 42, bar: 97}),
    set.has({bar: 97, foo: 42}),
    set.has("foo"),
    set.has("[\"foo\"]"),
    set.has(["fob"]),
    set.has({foo: 42}),
    set.has({fob: 42, bar: 97}),
    set.has({foo: 43, bar: 97}),
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
# Set as instance
set = ["foo", "bar", "baz"] | newSet;
[
    set | isInstance,
    classOf(set).name,
    set | display,
]
>> [true, "Set", "Set {elements: [\"foo\", \"bar\", \"baz\"]}"]
```

### Map|Map

A `Map` is a collection of key-value pairs, where keys are unique. Looking up a value by key is fast regardless of the size of the map.

#### newMap|newMap

Creates an immutable map.

Parameters:

- `entries` (_Array_, default `[]`): An array of `[key, value]` pairs. If duplicate keys exist, only the last one is kept.

Returns:

- (_Map_): The new map.

#### Map/size|Map-size

Returns:

- (_Number_): The number of key-value pairs in the map.

#### Map/keys|Map-keys

Returns:

- (_Array_): An array containing all keys in the map.

#### Map/values|Map-values

Returns:

- (_Array_): An array containing all values in the map.

#### Map/entries|Map-entries

Returns:

- (_Array_): An array of `[key, value]` pairs representing the map’s contents.

#### Map/has|Map-has

Checks whether the specified key exists in the map.

Parameters:

- `key` (_Any_): The key to check.

Returns:

- (_Boolean_): Whether the key is in the map.

#### Map/at|Map-at

Retrieves the value associated with the specified key.

Parameters:

- `key` (_Any_): The key whose value to retrieve.
- `default` (_Function or Null_, default `null`): A function that provides a default value if the key is not found, or `null` to throw an error if the key is missing.

Returns:

- (_Any_): The value associated with the key, or the result of calling `default` if the key is not found.

```
# Map
map = [["foo", 42], ["bar", 97]] | newMap;
[
    map.size(),
    map.keys(),
    map.values(),
    map.entries(),
    map.has("foo"),
    map.has("spam"),
    map.at("foo"),
    map.at("bar", default: $ 216),
    map.at("spam", default: $ 216),
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
    map.has(["foo"]),
    map.has({foo: 42, bar: 97}),
    map.has({bar: 97, foo: 42}),
    map.has("foo"),
    map.has("[\"foo\"]"),
    map.has(["fob"]),
    map.has({foo: 42}),
    map.has({fob: 42, bar: 97}),
    map.has({foo: 43, bar: 97}),
    map.at(["foo"]),
    map.at({bar: 97, foo: 42}),
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
# Map as instance
map = [["foo", 42], ["bar", 97]] | newMap;
[
    map | isInstance,
    classOf(map).name,
    map | display,
]
>> [true, "Map", "Map {entries: [[\"foo\", 42], [\"bar\", 97]]}"]
```

## Mutable Objects|mutable

### Var|Var

A `Var` holds a value that can be retrieved or updated.

#### newVar|newVar

Creates a mutable variable, set to the specified initial value.

Parameters:

- `initialValue` (_Any_): The initial value of the variable.

Returns:

- (_Var_): The new variable.

#### Var/get|Var-get

Returns the current value of the variable.

Returns:

- (_Any_): The current value.

#### Var/set|Var-set

Updates the variable's value.

Parameters:

- `newValue` (_Any_): The new value to assign.

Returns:

- (_Any_): The updated value.

```
# Variable creation, get, and set
var = newVar(42);
[
    var.get(),
    var.set(73),
    var.get(),
]
>> [42, 73, 73]
```

```
# Variable as instance
var = newVar(42);
[
    var | isInstance,
    classOf(var).name,
    var | display,
]
>> [true, "Var", "Var {value: 42}"]
```

### MutableArray|MutableArray

A mutable array allows updating arbitrary elements, adding new elements to the end, and removing elements from the end.

#### newMutableArray|newMutableArray

Creates a mutable array.

Parameters:

- `elements` (_Array_, default `[]`): The initial elements of the array.

Returns:

- (_MutableArray_): The new mutable array.

#### MutableArray/size|MutableArray-size

Returns:

- (_Number_): The number of elements in the array.

#### MutableArray/elements|MutableArray-elements

Returns:

- (_Array_): An array containing all elements in the mutable array.

#### MutableArray/append|MutableArray-append

Adds an element to the end of the array.

Parameters:

- `element` (_Any_): The value to append.

Returns:

- (_MutableArray_): The mutable array itself, for chaining.

#### MutableArray/set|MutableArray-set

Replaces the element at the specified index.

Parameters:

- `index` (_Number_): The position to update (1-based, negative values count from the end).
- `element` (_Any_): The new value.

Returns:

- (_MutableArray_): The mutable array itself, for chaining.

Throws:

- `indexOutOfBounds`: If the index is out of range.

#### MutableArray/storeAt|MutableArray-storeAt

Alias for `set`, but with parameters in reversed order. Useful as the final step in a pipeline that computes the new element value.

Parameters:

- `element` (_Any_): The new value.
- `index` (_Number_): The position to update (1-based, negative values count from the end).

Returns:

- (_MutableArray_): The mutable array itself, for chaining.

Throws:

- `indexOutOfBounds`: If the index is out of range.

#### MutableArray/at|MutableArray-at

Retrieves the element at the specified index.

Parameters:

- `index` (_Number_): The position to retrieve (1-based, negative values count from the end).
- `default:` (_Function or Null_, default `null`): A function returning a default value if the index is out of range, or `null` to throw an error.

Returns:

- (_Any_): The element at the index, or the result of `default` if out of range.

#### MutableArray/pop|MutableArray-pop

Removes and returns the last element.

Parameters:

- `default:` (_Function or Null_, default `null`): A function returning a default value if the array is empty, or `null` to throw an error.

Returns:

- (_Any_): The last element, or the result of `default` if the array is empty.

#### MutableArray/clear|MutableArray-clear

Removes all elements from the array.

Returns:

- (_MutableArray_): The mutable array itself, for chaining.

```
# Mutable array
array = ["foo", "bar", "baz"] | newMutableArray
|.append("spam")
|.append("eggs")
|.append("foo")
|.set(2, "toast")
|.storeAt("sausages", 4);
[
    array.size(),
    array.elements(),
    array.at(1),
    array.at(1, default: $ "boo"),
    array.at(7, default: $ "boo"),
    array.at(-2),
    array.pop(),
    array.size(),
    array.elements(),
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
# Mutable array as instance
array = ["foo", "bar", "baz"] | newMutableArray;
[
    array | isInstance,
    classOf(array).name,
    array | display,
]
>> [true, "MutableArray", "MutableArray {elements: [\"foo\", \"bar\", \"baz\"]}"]
```

### MutableSet|MutableSet

A mutable set is like a regular set, but it allows adding and removing elements dynamically.

#### newMutableSet|newMutableSet

Creates a mutable set.

Parameters:

- `elements` (_Array_, default `[]`): The initial elements of the set. Duplicate elements are silently discarded.

Returns:

- (_MutableSet_): The new mutable set.

#### MutableSet/size|MutableSet-size

Returns:

- (_Number_): The number of elements in the set.

#### MutableSet/elements|MutableSet-elements

Returns:

- (_Array_): An array containing all elements in the mutable set.

#### MutableSet/add|MutableSet-add

Adds an element to the set.

Parameters:

- `element` (_Any_): The value to add.

Returns:

- (_MutableSet_): The mutable set itself, for chaining.

#### MutableSet/remove|MutableSet-remove

Removes an element from the set.

Parameters:

- `element` (_Any_): The value to remove.

Returns:

- (_MutableSet_): The mutable set itself, for chaining.

#### MutableSet/has|MutableSet-has

Checks whether the specified element is in the set.

Parameters:

- `element` (_Any_): The value to look for.

Returns:

- (_Boolean_): Whether the value is in the set.

#### MutableSet/clear|MutableSet-clear

Removes all elements from the set.

Returns:

- (_MutableSet_): The mutable set itself, for chaining.

```
# Mutable set
set = ["foo", "bar", "baz"] | newMutableSet
|.add("spam")
|.add("eggs")
|.add("foo")
|.remove("bar")
|.remove("quux");
[
    set.size(),
    set.elements(),
    set.has("foo"),
    set.has("bar"),
    set.has("baz"),
    set.has("spam"),
    set.has("gorp"),
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
set = [["foo"]] | newMutableSet;
[
    set.has(["foo"]),
    set.has("foo"),
    set.has("[\"foo\"]"),
    set.has(["fob"]),
    set
    |.add({foo: 42, bar: 97})
    |.has({foo: 42, bar: 97}),
    set.has({bar: 97, foo: 42}),
    set.has({fob: 42, bar: 97}),
    set.has({foo: 43, bar: 97}),
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
# Mutable set as instance
set = ["foo", "bar", "baz"] | newMutableSet;
[
    set | isInstance,
    classOf(set).name,
    set | display,
]
>> [true, "MutableSet", "MutableSet {elements: [\"foo\", \"bar\", \"baz\"]}"]
```

### MutableMap|MutableMap

A mutable map is a collection of key-value pairs where keys are unique, and entries can be added, updated, and removed dynamically.

#### newMutableMap|newMutableMap

Creates a mutable map.

Parameters:

- `entries` (_Array_, default `[]`): An array of `[key, value]` pairs to initialize the map.

Returns:

- (_MutableMap_): The new mutable map.

#### MutableMap/size|MutableMap-size

Returns:

- (_Number_): The number of entries in the map.

#### MutableMap/keys|MutableMap-keys

Returns:

- (_Array_): An array containing all keys in the map.

#### MutableMap/values|MutableMap-values

Returns:

- (_Array_): An array containing all values in the map.

#### MutableMap/entries|MutableMap-entries

Returns:

- (_Array_): An array of `[key, value]` pairs representing the map's entries.

#### MutableMap/set|MutableMap-set

Sets the value for a given key.

Parameters:

- `key` (_Any_): The key to set.
- `value` (_Any_): The value to associate with the key.

Returns:

- (_MutableMap_): The mutable map itself, for chaining.

#### MutableMap/storeAt|MutableMap-storeAt

Alias for `set`, but with parameters in reversed order. Useful as the final step in a pipeline that computes the new element value.

Parameters:

- `value` (_Any_): The value to store.
- `key` (_Any_): The key to associate with the value.

Returns:

- (_MutableMap_): The mutable map itself, for chaining.

#### MutableMap/remove|MutableMap-remove

Removes a key and its associated value from the map.

Parameters:

- `key` (_Any_): The key to remove.

Returns:

- (_MutableMap_): The mutable map itself, for chaining.

#### MutableMap/has|MutableMap-has

Checks whether the map contains a specific key.

Parameters:

- `key` (_Any_): The key to check.

Returns:

- (_Boolean_): Whether the key exists in the map.

#### MutableMap/at|MutableMap-at

Retrieves the value associated with a given key.

Parameters:

- `key` (_Any_): The key to look up.
- `default:` (_Function or Null_, default `null`): A function returning a default value if the key is not found, or `Null` to throw an error.

Returns:

- (_Any_): The value associated with the key, or the default value if the key is not found.

#### MutableMap/clear|MutableMap-clear

Removes all entries from the map.

Returns:

- (_MutableMap_): The mutable map itself, for chaining.

```
# Mutable map
map = [["foo", 42], ["bar", 97]] | newMutableMap
|.set("eggs", 216)
|.storeAt(729, "foo")
|.remove("bar");
[
    map.size(),
    map.keys(),
    map.values(),
    map.entries(),
    map.has("foo"),
    map.has("bar"),
    map.at("foo"),
    map.at("eggs", default: $ 57),
    map.at("bar", default: $ 57),
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
map = [[["foo"], 42]] | newMutableMap;
[
    map.has(["foo"]),
    map.has("foo"),
    map.has("[\"foo\"]"),
    map.has(["fob"]),
    map.at(["foo"]),
    map
    |.set({foo: 42, bar: 97}, 97)
    |.has({foo: 42, bar: 97}),
    map.has({bar: 97, foo: 42}),
    map.has({fob: 42, bar: 97}),
    map.has({foo: 43, bar: 97}),
    map.at({bar: 97, foo: 42}),
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
# Mutable map as instance
map = [["foo", 42], ["bar", 97]] | newMutableMap;
[
    map | isInstance,
    classOf(map).name,
    map | display,
]
>> [true, "MutableMap", "MutableMap {entries: [[\"foo\", 42], [\"bar\", 97]]}"]
```

### also

Performs an action on a value, then returns it for further processing.

Parameters:

- `value` (_Any_): The value to perform the action on.
- `action` (_Function_): The function to apply.

Returns:

- (_Any_): The `value` argument.

```
# Doing side effects on an expression result as it flies by
array = newMutableArray();
[
    42 | also((n) => array.append(n)),
    array.elements(),
]
>> [42, [42]]
```

## Errors|errors

### newError|newError

Creates an error value with the specified error type and details. This in itself doesn't _throw_ the error.

Parameters:

- `type` (_String_): The kind of error condition that this error represents.
- `**details` (_Object_): Any relevant information about the cause of the error.

Returns:

- (_Error_): The error value, an `Instance` with the specified `type` and `details` properties.

```
# Creation and destructuring
{type:, details:} = newError("badIdea", foo: "bar", spam: "eggs");
[type, details]
>> ["badIdea", {foo: "bar", spam: "eggs"}]
```

### throw|throw

Throws an error.

Parameters:

- `error` (_Error_): The error to throw.

Returns:

- (_Never_): Never returns.

```
# Throwing an error
throw(newError("badIdea", foo: "bar", spam: "eggs"))
!! badIdea {"foo": "bar", "spam": "eggs"}
```

### try|try

Calls the specified function, then invokes one of the specified handlers depending on whether the function threw an error.

Parameters:

- `f` (_Function_): The function to call.
- `onError:` (_Function_): The handler to invoke if the function throws an error.
- `onSuccess:` (_Function or Null_, default `null`): The handler to invoke if the function returns normally, or `null` to simply return the function's result.

```
# Trying a function
explode = $ throw(newError("badIdea"));
[
    try($ 42, onSuccess: up, onError: |.type),
    try(explode, onSuccess: up, onError: |.type),
    try($ 42, onError: |.type),
    try(explode, onError: |.type),
]
>> [43, "badIdea", 42, "badIdea"]
```

### catch|catch

Calls the specified function, returning `{status: "success", value: <value>}` if the function returns normally, or `{status: "error", error: <error>}` if the function throws an error.

This is useful if the result of a fallable operation needs to be stored somewhere for later processing.

Parameters:

- `f` (_Function_): The function to call.

Returns:

- (_Object_): An object with the status and value or error.

```
# Catching errors
[
    catch($ 42),
    catch($ throw(newError("badIdea")))
    | (({status:, error:}) => {status:, error: error.type}),
]
>> [
    {status: "success", value: 42},
    {status: "error", error: "badIdea"}
]
```

## Validation|validation

Kenpali is a dynamically typed language, but it does strict type checks at runtime. The core module exposes several functions to interact with the type checking system.

Values are checked against a _schema_. Valid schemas are:

- A Kenpali type: one of the primitive classes `Null`, `Boolean`, `Number`, `String`, `Array`, `Object`, or `Function`; or an instance type such as `Error`, `Class`, or `Map`; or a protocol such as `Sequence` or `Type`.
- An object of the form `{form: "enum", values: [<values>]}`, which matches only the specified values.
- An object of the form `{form: "union", options: [<schemas>]}`, which matches a value if _at least one of_ the specified schemas matches it.
- An object of the form `{form: "condition", schema: <schema>, condition: <function>}`, which adds the specified condition to the schema. For example, `{form: "condition", schema: Number, condition: | lt(10)}` matches only numbers less than 10.
- An object of the form `{form: "array", elements: <schema>}`, which matches only arrays whose elements _all_ match the specified schema. For example, `{form: "array", elements: Number}` matches only arrays of numbers.
- An object of the form `{form: "tuple", shape: [<schemas>]}`, which matches only arrays whose elements match the specified schemas in order. For example, `{form: "tuple", shape: [Number, String]}` matches only arrays whose first element is a number and whose second element is a string.
- An object of the form `{form: "object", keys: <key-schema>, values: <value-schema>}`, which matches only objects whose keys and values match the specified schemas. Either `keys` or `values` may be omitted; `keys` defaults to `String`, and `values` defaults to `Any`. For example, `{form: "object", values: Number}` matches only objects whose whose values are numbers, while `{form: "object", keys: {form: "enum", values: ["foo", "bar", "baz"]}, values: Number}` matches only objects whose keys are some subset of `"foo"`, `"bar"`, and `"baz"` and whose values are numbers.
- An object of the form `{form: "record", shape: {<property-schemas>}}`, where each property of the shape is a schema that the corresponding property must match. For example, `{form: "record", shape: {foo: Number, bar: String}}` matches only objects with a numeric `foo` property and a string `bar` property.

### validate|validate

Ensures that the specified value matches a schema. Throws a validation error if the value doesn't match.

Parameters:

- `value` (_Any_): The value to check.
- `schema` (_Type or Object_): The schema to check against.

Returns:

- (_Boolean_): Always `true`.

### matches|matches

Checks whether the specified value matches a schema.

Parameters:

- `value` (_Any_): The value to check.
- `schema` (_Type or Object_): The schema to check against.

Returns:

- (_Boolean_): Whether the value matches the schema.

```
# Matching classOf results
[
    null | matches(Null),
    false | matches(Null),
    false | matches(Boolean),
    42 | matches(Boolean),
    42 | matches(Number),
    "foo" | matches(Number),
    "foo" | matches(String),
    [1, 2, 3] | matches(String),
    [1, 2, 3] | matches(Array),
    {foo: 1, bar: 2} | matches(Array),
    {foo: 1, bar: 2} | matches(Object),
    classOf | matches(Object),
    classOf | matches(Function),
    ((x) => x) | matches(Object),
    ((x) => x) | matches(Function),
    newError("badIdea") | matches(Function),
    newError("badIdea") | matches(Error),
    null | matches(Error),
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
# Matching the protocols `Sequence`, `Type`, and `Any`
[
    "foo" | matches(Sequence),
    [1, 2, 3] | matches(Sequence),
    {foo: 1, bar: 2} | matches(Sequence),
    classOf | matches(Sequence),
    ((x) => x) | matches(Sequence),
    newError("badIdea") | matches(Sequence),
    Number | matches(Sequence),
    Sequence | matches(Sequence),
    "foo" | matches(Type),
    [1, 2, 3] | matches(Type),
    {foo: 1, bar: 2} | matches(Type),
    classOf | matches(Type),
    ((x) => x) | matches(Type),
    newError("badIdea") | matches(Type),
    Number | matches(Type),
    Sequence | matches(Type),
    "foo" | matches(Any),
    [1, 2, 3] | matches(Any),
    {foo: 1, bar: 2} | matches(Any),
    classOf | matches(Any),
    ((x) => x) | matches(Any),
    newError("badIdea") | matches(Any),
    Number | matches(Any),
    Sequence | matches(Any),
]
>> [
    true,
    true,
    false,
    false,
    false,
    false,
    false,
    false,
    false,
    false,
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
    true,
    true,
    true,
    true,
]
```

### oneOfValues|oneOfValues

Creates a schema that checks if the value is in the specified list. This can be used to simulate an "enum" type.

Parametersː

- `*values` (_Array of Any_): The allowed values.

Returns:

- (_Object_): A schema of the form `{form: "enum", values: [<values>]}`.

```
# Matching a list of specific values
sch = oneOfValues("red", "green", "blue");
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

### either|either

Creates a schema that allows anything matching any of the specified schemas.

Parameters:

- `*schemas` (_Array of Type or Object_): The allowed schemas.

Returns:

- (_Object_): A schema of the form `{form: "union", options: [<schemas>]}`.

```
# Matching a union
sch = either(String, Number);
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

### satisfying|satisfying

Creates a `condition` schema, adding the specified predicate to the schema.

Parameters:

- `schema` (_Type or Object_): The schema to add the condition to.
- `condition` (_Function_): A predicate that the value must satisfy.

Returns:

- (_Object_): A schema of the form `{form: "condition", schema: <schema>, condition: <function>}`.

```
# Matching a type with a predicate
sch = Number | satisfying(| lt(10));
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

### arrayOf|arrayOf

Creates a schema that checks all the elements of an array against a single schema.

Parameters:

- `elements` (_Type or Object_): The schema that all elements must match.

Returns:

- (_Object_): A schema of the form `{form: "array", elements: <schema>}`.

```
# Matching an array where all elements must be of a given type
sch = arrayOf(String);
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

### tupleLike|tupleLike

Creates a schema that checks each element of an array against a separate schema.

Parameters:

- `shape` (_Array of Type or Object_): A schema that each element of the array must match.

Returns:

- (_Object_): A schema of the form `{form: "tuple", shape: [<schemas>]}`.

```
# Matching an array where each element has its own schema
sch = tupleLike([String, Number, arrayOf(Number)]);
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

### objectOf|objectOf

Creates a schema that checks the property values of an object against a single schema, and optionally that the keys match a schema and.

Parameters:

- `keys:` (_Type or Object_, default: `String`): The schema that all keys must match.
- `values:` (_Type or Object_): The schema that all values must match.

Returns:

- (_Object_): An object of the form `{form: "object", keys: <key-schema>, values: <value-schema>}`.

```
# Matching an object where keys and values must match schemas
sch = objectOf(keys: String | satisfying(| length | eq(1)), values: Number);
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

### recordLike|recordLike

Creates a schema that checks each property of an object against a separate schema.

Parameters:

- `shape` (_Object of Type or Object_): A schema that each property of the object must match.

Returns:

- (_Object_): A schema of the form `{form: "record", shape: {<entry-schemas>}}`.

```
# Matching an object with a specific structure
person = recordLike({name: String, age: Number});
[
    {name: "John", age: 42} | matches(person),
    {name: "John", age: 42, job: "doctor"} | matches(person),
    {name: "John"} | matches(person),
    {name: "John", age: "middle"} | matches(person),
    {name: 42} | matches(person),
    {age: 42} | matches(person),
    42 | matches(person),
]
>> [
    true,
    true,
    false,
    false,
    false,
    false,
    false,
]
```

### optional|optional

Creates a schema that marks a tuple element or record property as optional.

The returned schema is only valid inside the `shape` property of a tuple or record schema.

Parameters:

- `schema` (_Type or Object_): The schema that the element or property must match if present.

Returns:

- (_Object_): A schema of the form `{form: "optional", schema: <schema>}`.

```
# Matching an array with an optional element
sch = tupleLike([String, Number, optional(arrayOf(Number))]);
[
    ["foo", 42, [1, 2, 3]] | matches(sch),
    ["foo", 42, [], "extra"] | matches(sch),
    ["foo", 42] | matches(sch),
    ["foo", "bar", [1, 2, 3]] | matches(sch),
    ["foo"] | matches(sch),
    42 | matches(sch),
]
>> [
    true,
    true,
    true,
    false,
    false,
    false,
]
```

```
# Matching an object with an optional property
person = recordLike({name: String, age: optional(Number)});
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
