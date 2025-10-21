# Kenpali Core Specification

The Core functions must be available to any Kenpali program. Reference implementations for some of them in terms of the others are provided by `core.kpc`, enabling a complete implementation with fewer platform functions.

## Arithmetic|arithmetic

### plus|plus

Returns the sum of its arguments

Parameters:

- `*numbers` (_Array of Number_): The numbers to add up.

Returns:

- (_Number_): The sum.


```
# Plus
[plus(), plus(1), plus(1, 2), plus(1, 2, 3)]
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

### minus|minus

Returns its first argument minus its second argument.

Parameters:

- `a` (_Number_): The amount to subtract from.
- `b` (_Number_): The amount to take away.

Returns:

- (_Number_): The difference.

```
# Minus
[5 | minus(2), 2 | minus(5)]
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

### times|times

Returns the product of its arguments.

Parameters:

- `*numbers` (_Array of Number_): The values to multiply.

Returns:

- (_Number_): The product.

```
# Times
[times(), times(2), times(2, 3), times(2, 3, 4)]
>> [1, 2, 6, 24]
```

### dividedBy|dividedBy

Returns its first argument divided by its second argument.

Parameters:

- `a` (_Number_): The dividend.
- `b` (_Number_): The divisor.

Returns:

- (_Number_): The quotient.

```
# Divided by
[12 | dividedBy(3), 12 | dividedBy(8)]
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
toCodePoints("foo\u1234")
>> [102, 111, 111, 4660]
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
- If both are arrays, they must have the same length, and corresponding elements must be equal according to the `equals` function.
- If both are objects, they must have the same property names, and corresponding property values must be equal according to the `equals` function. The order in which the properties are written does not affect equality.
- Otherwise, they must be aliases referring to the same data in memory.

Ordering comparisons work on booleans, numbers, strings, and arrays, as follows:

- For booleans, `false` is less than `true`.
- Numbers are compared by numerical value.
- Strings are compared lexicographically by their Unicode code points.
- Arrays are compared lexicographically by their elements.

### equals|equals

Returns whether its arguments have equal values, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Any_): The first value.
- `b` (_Any_): The second value.

Returns:

- (_Boolean_): Whether the values are equal.

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

### isLessThan|isLessThan

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

### isAtMost|isAtMost

Returns whether its first argument is less than or equal to its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is less than or equal to `b`.

```
# At most (less than or equal)
[
    42 | isAtMost(43),
    43 | isAtMost(43),
    43 | isAtMost(42),
]
>> [true, true, false]
```

### isMoreThan|isMoreThan

Returns whether its first argument is greater than its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is greater than `b`.

```
# More than (greater than)
[
    42 | isMoreThan(43),
    43 | isMoreThan(43),
    43 | isMoreThan(42),
]
>> [false, false, true]
```

### isAtLeast|isAtLeast

Returns whether its first argument is greater than or equal to its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is greater than or equal to `b`.

```
# At least (greater than or equal)
[
    42 | isAtLeast(43),
    43 | isAtLeast(43),
    43 | isAtLeast(42),
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

### most|most

Returns the greatest element in the specified sequence, i.e. the element that is greater than or equal to all other elements, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `sequence` (_Sequence_): The values to find the greatest element of.

Returns:

- (_Number, String, Boolean, or Array_): The greatest element.

```
# Most
[97, 42, 216] | most
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
    classOf((1 @ 1)!),
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
    isNull((1 @ 1)!),
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
    isBoolean((1 @ 1)!),
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
    isNumber((1 @ 1)!),
    isNumber(Number),
    isNumber(Sequence),
]
>> [false, false, false, true, false, false, false, false, false, false, false, false, false]
```

### toNumber|toNumber

Returns the numeric representation of its argument.

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
    isString((1 @ 1)!),
    isString(Number),
    isString(Sequence),
]
>> [false, false, false, false, true, false, false, false, false, false, false, false, false]
```

### toString|toString

Returns the string representation of its argument.

Calling `toString` on a stream reports already evaluated elements but never forces further evaluation.

Parameters:

- `value` (_Any_): The value to convert.

Returns:

- (_String_): The string value.

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
    toString(Number),
    toString(Sequence),
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
    "Function {name: \"toString\"}",
    "Error {type: \"wrongType\", details: {value: 1, expectedType: \"either(Sequence, Object, Instance)\"}, calls: []}",
    "Class {name: \"Number\"}",
    "Protocol {name: \"Sequence\"}",
]
```

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
    "Stream [...]",
    "Stream [1, 2, 3...]",
    "Stream [1, 2, 3, 4]",
]
```

```
# To string on natural functions
foo = $ (
    bar = $ 42;
    $ bar
);
[
    ($ 42) | toString,
    foo | toString,
    foo() | toString,
    foo()() | toString,
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
    isArray((1 @ 1)!),
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
    isStream((1 @ 1)!),
    isStream(Number),
    isStream(Sequence),
]
>> [false, false, false, false, false, false, true, false, false, false, false, false, false]
```

### toStream|toStream

Returns a stream that iterates over the elements of the specified sequence.

If the argument is already a stream, the same stream is returned, rather than a wrapper, i.e. `x | toStream | equals(x)` is true if `x` is a stream.

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
    isObject((1 @ 1)!),
    isObject(Number),
    isObject(Sequence),
]
>> [false, false, false, false, false, false, false, true, false, false, false, false, false]
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
    isFunction((1 @ 1)!),
    isFunction(Number),
    isFunction(Sequence),
]
>> [false, false, false, false, false, false, false, false, true, true, false, false, false]
```

### isError|isError

Returns whether its argument is an error.

Note that merely calling this function doesn't _catch_ a thrown error: if `x` throws, `x | isError` propagates the error rather than returning `true`. The error must be explicitly caught first, i.e. `x ! | isError`.

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
    isError((1 @ 1)!),
    isError(Number),
    isError(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, true, false, false]
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
    toFunction(toString)(97),
    toFunction((x) => plus(x, 3))(97),
]
>> [42, "97", 100]
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
    isClass((1 @ 1)!),
    isClass(Number),
    isClass(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, true, false]
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
    isSequence((1 @ 1)!),
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
    isType((1 @ 1)!),
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
    isInstance((1 @ 1)!),
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
    42 | butIf(| isMoreThan(10), | minus(5)),
    7 | butIf(| isMoreThan(10), | minus(5)),
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
    [$ a | isLessThan(b), $ "Too small!"],
    [$ a | isMoreThan(b), $ "Too big!"],
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

## Stream Builders|stream-builders

These functions build up new streams from scalar inputs.

### build|build

Generates a stream by repeatedly applying a function to a start value.

Parameters:

- `start` (_Any_): The start value.
- `next` (_Function_): A function that computes the next state from the current state.

Returns:

- (_Stream_): A stream containing `start` and all values produced by the `next` function.

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

### newStream|newStream

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
    next: $ myStream(start | times(2))
);
1 | myStream | keepFirst(5) | toArray
>> [1, 2, 4, 8, 16]
```

### emptyStream|emptyStream

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
    start | isLessThan(100),
    then: $ newStream(
        value: $ start,
        next: $ myStream(start | times(2))
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
    emptyStream() |.isEmpty(),
    (1 | to(3)) |.isEmpty(),
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
    emptyStream() |.value() ! |.type,
    (1 | to(3)) |.value(),
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
    emptyStream() |.next() ! |.type,
    (1 | to(3)) |.next() | toArray,
]
>> ["missingProperty", [2, 3]]
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
    [1, 10, 2, 9, 3, 12] | count((i) => (i | isLessThan(10))),
    [1, 10, 2, 9, 3, 12] | toStream | count((i) => (i | isLessThan(10))),
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
    [1, 2, 3] | forAll((n) => (n | isLessThan(10))),
    [1, 42, 3] | forAll((n) => (n | isLessThan(10))),
    [1, 2, 3] | toStream | forAll((n) => (n | isLessThan(10))),
    [1, 42, 3] | toStream | forAll((n) => (n | isLessThan(10))),
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
    [41, 2, 43] | forSome((n) => (n | isLessThan(10))),
    [41, 42, 43] | forSome((n) => (n | isLessThan(10))),
    [41, 2, 43] | toStream | forSome((n) => (n | isLessThan(10))),
    [41, 42, 43] | toStream | forSome((n) => (n | isLessThan(10))),
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
result = mutableArray();
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
    1 | build(| times(2)) | first,
]
>> ["foo", "foo", 1, 1]
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
        total | times(10) | plus(number)
    ),
)
| toArray
>> [0, 2, 28, 289, 2893, 28937]
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
    1 | build(| times(2)) | keepFirst(3) | toArray,
]
>> [
    "foo",
    [42, 97, 6],
    [1, 2, 4],
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
    1 | build(| times(2)) | dropFirst(3) | keepFirst(3) | toArray,
]
>> [
    "oobar",
    "obar",
    [97, 6, 12, 64],
    [12, 64],
    [8, 16, 32],
]
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
    1 | build(| times(2)) | slice(from: 2, to: 4) | toArray,
]
>> [
    "oob",
    "oobar",
    "foob",
    [97, 6, 12],
    [97, 6, 12, 64],
    [42, 97, 6, 12],
    [2, 4, 8]
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
| build(| times(2))
| while(| isLessThan(100))
| toArray
>> [1, 2, 4, 8, 16, 32, 64]
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
| build(| times(2))
| continueIf(| isLessThan(100))
| toArray
>> [1, 2, 4, 8, 16, 32, 64, 128]
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

### flatten|flatten

Flattens a sequence of sequences into a single stream.

Parameters:

- `sequences` (_Sequence of Sequence_): The sequence to flatten.

Returns:

- (_Stream_): A stream containing all elements from the nested sequences.

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
    1 | build(| times(2)) | chunk(3) | keepFirst(3) | toArray,
]
>> [
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11]],
    [[1, 2, 4], [8, 16, 32], [64, 128, 256]],
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

### toObject|toObject

Converts the specified value into an object.

If the value is an array or stream, its elements are treated as key-value pairs. Any duplicate keys are assigned the value from the _last_ pair with that key in the sequence.

If the value is an error, an object containing the error details is returned.

Parameters:

- `value` (_Array or Stream or Error_): The value to convert. If an array or stream, it must contain tuples like _[string, any]_.

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
# Object from error
1 @ 1 ! | toObject
>> {"#class": "Error", type: "wrongType", details: {value: 1, expectedType: "either(Sequence, Object, Instance)"}, calls: []}
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
    ["foo", "bar", "baz"] | at(2),
    ["foo", "bar", "baz"] | at(2, default: $ "other"),
    ["foo", "bar", "baz"] | at(-2),
    ["foo", "bar", "baz"] | at(-2, default: $ "other"),
    ["foo", "bar", "baz"] | at(4, default: $ "other"),
    ["foo", "bar", "baz"] | at(0, default: $ "other"),
    ["foo", "bar", "baz"] | at(-4, default: $ "other"),
]
>> ["bar", "bar", "bar", "bar", "other", "other", "other"]
```

```
# Indexing finite streams
[
    2 | to(5) | at(2),
    2 | to(5) | at(2, default: $ 42),
    2 | to(5) | at(-2),
    2 | to(5) | at(-2, default: $ 42),
    2 | to(5) | at(5, default: $ 42),
    2 | to(5) | at(0, default: $ 42),
    2 | to(5) | at(-5, default: $ 42),
]
>> [3, 3, 4, 4, 42, 42, 42]
```

```
# Indexing infinite streams
[
    2 | build(| times(2)) | at(2),
    2 | build(| times(2)) | at(2, default: $ 42),
    2 | build(| times(2)) | at(0, default: $ 42),
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
1 | build(| times(2) | debug) @ 10 // Open the console to see the debug output!
>> 512
```

### itself|itself

Returns its argument. Useful when a function must be provided, but no transformation is needed.

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

## Sets and Maps|sets-maps

### newSet|newSet

Creates an immutable set.

A set is like an array, but it can't have duplicate elements, and checking whether an element is in the set is fast regardless of the size of the set.

Parameters:

- `elements` (_Array_, default `[]`): The set's elements. Duplicate elements are silently discarded.

Returns:

- (_Object_): The new set.

#### newSet/size|newSet-size

Returns:

- (_Number_): The number of elements in the set.

#### newSet/elements|newSet-elements

Returns:

- (_Array_): An array containing all the elements in the set.

#### newSet/has|newSet-has

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

### newMap|newMap

Creates an immutable map.

A map is a collection of key-value pairs, where keys are unique. Looking up a value by key is fast regardless of the size of the map.

Parameters:

- `entries` (_array_, default `[]`): An array of `[key, value]` pairs. If duplicate keys exist, only the last one is kept.

Returns:

- (_object_): The new map.

#### newMap/size|newMap-size

Returns:

- (_number_): The number of key-value pairs in the map.

#### newMap/keys|newMap-keys

Returns:

- (_Array_): An array containing all keys in the map.

#### newMap/values|newMap-values

Returns:

- (_Array_): An array containing all values in the map.

#### newMap/entries|newMap-entries

Returns:

- (_Array_): An array of `[key, value]` pairs representing the map’s contents.

#### newMap/has|newMap-has

Checks whether the specified key exists in the map.

Parameters:

- `key` (_Any_): The key to check.

Returns:

- (_Boolean_): Whether the key is in the map.

#### newMap/at|newMap-at

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
    classOf(var) |.name,
    var | toString,
]
>> [true, "Var", "Var {value: 42}"]
```

### mutableArray|mutableArray

Creates a mutable array.

A mutable array allows updating arbitrary elements, adding new elements to the end, and removing elements from the end.

Parameters:

- `elements` (_array_, default `[]`): The initial elements of the array.

Returns:

- (_Object_): The new mutable array.

#### mutableArray/size|mutableArray-size

Returns:

- (_Number_): The number of elements in the array.

#### mutableArray/elements|mutableArray-elements

Returns:

- (_Array_): An array containing all elements in the mutable array.

#### mutableArray/append|mutableArray-append

Adds an element to the end of the array.

Parameters:

- `element` (_Any_): The value to append.

Returns:

- (_Object_): The mutable array itself, for chaining.

#### mutableArray/set|mutableArray-set

Replaces the element at the specified index.

Parameters:

- `index` (_Number_): The position to update (1-based, negative values count from the end).
- `element` (_Any_): The new value.

Returns:

- (_Object_): The mutable array itself, for chaining.

Throws:

- `indexOutOfBounds`: If the index is out of range.

#### mutableArray/storeAt|mutableArray-storeAt

Alias for `set`, but with parameters in reversed order. Useful as the final step in a pipeline that computes the new element value.

Parameters:

- `element` (_Any_): The new value.
- `index` (_Number_): The position to update (1-based, negative values count from the end).

Returns:

- (_Object_): The mutable array itself, for chaining.

Throws:

- `indexOutOfBounds`: If the index is out of range.

#### mutableArray/at|mutableArray-at

Retrieves the element at the specified index.

Parameters:

- `index` (_Number_): The position to retrieve (1-based, negative values count from the end).
- `default:` (_Function or Null_, default `null`): A function returning a default value if the index is out of range, or `null` to throw an error.

Returns:

- (_Any_): The element at the index, or the result of `default` if out of range.

#### mutableArray/pop|mutableArray-pop

Removes and returns the last element.

Parameters:

- `default:` (_Function or Null_, default `null`): A function returning a default value if the array is empty, or `null` to throw an error.

Returns:

- (_Any_): The last element, or the result of `default` if the array is empty.

#### mutableArray/clear|mutableArray-clear

Removes all elements from the array.

Returns:

- (_Object_): The mutable array itself, for chaining.

```
# Mutable array
array = ["foo", "bar", "baz"] | mutableArray
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

### mutableSet|mutableSet

Creates a mutable set.

A mutable set is like a regular set, but it allows adding and removing elements dynamically.

Parameters:

- `elements` (_Array_, default `[]`): The initial elements of the set. Duplicate elements are silently discarded.

Returns:

- (_Object_): The new mutable set.

#### mutableSet/size|mutableSet-size

Returns:

- (_Number_): The number of elements in the set.

#### mutableSet/elements|mutableSet-elements

Returns:

- (_Array_): An array containing all elements in the mutable set.

#### mutableSet/add|mutableSet-add

Adds an element to the set.

Parameters:

- `element` (_Any_): The value to add.

Returns:

- (_Object_): The mutable set itself, for chaining.

#### mutableSet/remove|mutableSet-remove

Removes an element from the set.

Parameters:

- `element` (_Any_): The value to remove.

Returns:

- (_Object_): The mutable set itself, for chaining.

#### mutableSet/has|mutableSet-has

Checks whether the specified element is in the set.

Parameters:

- `element` (_Any_): The value to look for.

Returns:

- (_Boolean_): Whether the value is in the set.

#### mutableSet/clear|mutableSet-clear

Removes all elements from the set.

Returns:

- (_Object_): The mutable set itself, for chaining.

```
# Mutable set
set = ["foo", "bar", "baz"] | mutableSet
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
set = [["foo"]] | mutableSet;
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

### mutableMap|mutableMap

Creates a mutable map.

A mutable map is a collection of key-value pairs where keys are unique, and entries can be added, updated, and removed dynamically.

Parameters:

- `entries` (_Array_, default `[]`): An array of `[key, value]` pairs to initialize the map.

Returns:

- (_Object_): The new mutable map.

#### mutableMap/size|mutableMap-size

Returns:

- (_Number_): The number of entries in the map.

#### mutableMap/keys|mutableMap-keys

Returns:

- (_Array_): An array containing all keys in the map.

#### mutableMap/values|mutableMap-values

Returns:

- (_Array_): An array containing all values in the map.

#### mutableMap/entries|mutableMap-entries

Returns:

- (_Array_): An array of `[key, value]` pairs representing the map's entries.

#### mutableMap/set|mutableMap-set

Sets the value for a given key.

Parameters:

- `key` (_Any_): The key to set.
- `value` (_Any_): The value to associate with the key.

Returns:

- (_Object_): The mutable map itself, for chaining.

#### mutableMap/storeAt|mutableMap-storeAt

Alias for `set`, but with parameters in reversed order. Useful as the final step in a pipeline that computes the new element value.

Parameters:

- `value` (_Any_): The value to store.
- `key` (_Any_): The key to associate with the value.

Returns:

- (_Object_): The mutable map itself, for chaining.

#### mutableMap/remove|mutableMap-remove

Removes a key and its associated value from the map.

Parameters:

- `key` (_Any_): The key to remove.

Returns:

- (_Object_): The mutable map itself, for chaining.

#### mutableMap/has|mutableMap-has

Checks whether the map contains a specific key.

Parameters:

- `key` (_Any_): The key to check.

Returns:

- (_Boolean_): Whether the key exists in the map.

#### mutableMap/at|mutableMap-at

Retrieves the value associated with a given key.

Parameters:

- `key` (_Any_): The key to look up.
- `default:` (_Function or Null_, default `null`): A function returning a default value if the key is not found, or `Null` to throw an error.

Returns:

- (_Any_): The value associated with the key, or the default value if the key is not found.

#### mutableMap/clear|mutableMap-clear

Removes all entries from the map.

Returns:

- (_Object_): The mutable map itself, for chaining.

```
# Mutable map
map = [["foo", 42], ["bar", 97]] | mutableMap
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
map = [[["foo"], 42]] | mutableMap;
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

### also

Performs an action on a value, then returns it for further processing.

Parameters:

- `value` (_Any_): The value to perform the action on.
- `action` (_Function_): The function to apply.

Returns:

- (_Any_): The `value` argument.

```
# Doing side effects on an expression result as it flies by
array = mutableArray();
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

## Validation|validation

Kenpali is a dynamically typed language, but it does strict type checks at runtime. The core module exposes several functions to interact with the type checking system.

Values are checked against a _schema_. Valid schemas are:

- A Kenpali type (class or protocol), one of `Null`, `Boolean`, `Number`, `String`, `Array`, `Stream`, `Object`, `Function`, `Error`, `Class`, `Protocol`, `Sequence`, `Type`, `Instance`, `Any`.
- An object of the form `{oneOf: [<values>]}`, which matches only the specified values.
- An object of the form `{either: [<schemas>]}`, which matches a value if _at least one of_ the specified schemas matches it.
- An object of the form `{type: <type>, ...}`. On its own, this is equivalent to a type schema, e.g. `{type: Number}` matches the same values as `Number`. But this format allows additional properties to narrow the range of accepted values:
    - For arrays, the `elements` property specifies the schema that all the array's elements must match, e.g. `{type: Array, elements: Number}` matches only array of numbers.
    - For arrays, the `shape` property specifies an array of individual schemas for each element, e.g. `{type: Array, shape: [Number, String]}` matches only arrays whose first element is a number and whose second element is a string.
    - For objects, the `values` property specifies the schemas that all the object's property values must match, e.g. `{type: Object, values: Number}` matches only objects whose properties are all numbers.
    - For objects, the `shape` property specifies an object whose properties are schemas that the corresponding properties must match, e.g. `{type: Object, shape: {foo: Number, bar: String}}` matches only objects with a numeric `foo` property and a string `bar` property.
    - The `where` property specifies an arbitrary function that must return `true` when called on the value, e.g. `{type: Number, where: | isLessThan(10)}` matches only numbers less than 10.

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
    (1 @ 1)! |  matches(Function),
    (1 @ 1)! | matches(Error),
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
    (1 @ 1)! | matches(Sequence),
    Number | matches(Sequence),
    Sequence | matches(Sequence),
    "foo" | matches(Type),
    [1, 2, 3] | matches(Type),
    {foo: 1, bar: 2} | matches(Type),
    classOf | matches(Type),
    ((x) => x) | matches(Type),
    (1 @ 1)! | matches(Type),
    Number | matches(Type),
    Sequence | matches(Type),
    "foo" | matches(Any),
    [1, 2, 3] | matches(Any),
    {foo: 1, bar: 2} | matches(Any),
    classOf | matches(Any),
    ((x) => x) | matches(Any),
    (1 @ 1)! | matches(Any),
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

### is|is

Creates a schema that checks the type of the value, and optionally that it satisfies a predicate.

Parameters:

- `type` (_Type_): The expected Kenpali type.
- `where:` (_Function or Null_, default `null`): A predicate that the value must satisfy.

Returns:

- (_Object_): A schema of the form `{type, where}`.

```
# Matching a type with a predicate
sch = is(Number, where: (n) => isLessThan(n, 10));
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

### oneOf|oneOf

Creates a schema that checks if the value is in the specified list.

Parametersː

- `*values` (_Array of Any_): The allowed values.

Returns:

- (_Object_): A schema of the form `{oneOf: values}`.

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

### arrayOf|arrayOf

Creates a schema that checks the elements of an array against a single schema, and optionally that the array satisfies a predicate.

Parameters:

- `elementSchema` (_Type or Object_): The schema that all elements must match.
- `where:` (_Function or Null_, default `null`): A predicate that the array must satisfy.

Returns:

- (_Object_): A schema of the form `{type: "array", elements: elementSchema, where}`.

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

- (_Object_): A schema of the form `{type: "array", shape}`.

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

Creates a schema that checks the property values of an object against a single schema, and optionally that the keys match a schema and/or that the entire object satisfies a predicate.

Parameters:

- `keys:` (_Type or Object_, default: `String`): The schema that all keys must match.
- `values:` (_Type or Object_): The schema that all values must match.
- `where:` (_Function or Null_, default `null`): A predicate that the object must satisfy.

Returns:

- (_Object_): An object of the form `{type: "object", keys, values, where}`.

```
# Matching an object where keys and values must match schemas
sch = objectOf(keys: is(String, where: (s) => (length(s) | equals(1))), values: Number);
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

- (_Object_): A schema of the form `{type: "object", shape}`.

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

Creates a schema that marks an array element or object property as optional.

The returned schema is only valid inside the `shape` property of an array or object schema.

Parameters:

- `schema` (_Type or Object_): The schema that the element or property must match if present.

Returns:

- (_Object_): A schema of the form `{optional: schema}`.

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

### either|either

Creates a schema that allows anything matching any of the specified schemas.

Parameters:

- `*schemas` (_Array of Type or Object_): The allowed schemas.

Returns:

- (_Object_): A schema of the form `{either: schemas}`.

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
