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

Returns the sum of the values in the specified collection.

Parameters:

- `numbers` (_Collection of Number_): The numbers to add up.

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

The quotient is always rounded down for a positive divisor, and rounded up for a negative divisor.

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

- `strings` (_Sequence of String_): The values to join.
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

- `strings` (_Sequence of String_): The values to join.

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

All comparisons are strict about types: testing values of different classes for equality always returns `false`, while using ordering comparisons on different classes always throws a `wrongArgumentType` error.

Any two values can be tested for equality. To be considered equal, the values must have the same class, _and_:

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
# Equality of null
eq(null, null)
>> true
```

```
# Equality of booleans
[eq(true, true), eq(false, true), eq(false, false)]
>> [true, false, true]
```

```
# Equality of numbers
[eq(42, 42), eq(42, 43)]
>> [true, false]
```

```
# Equality of strings
[eq("bar", "bar"), eq("bar", "baz")]
>> [true, false]
```

```
# Equality of arrays
[
    eq(["foo", "bar"], ["foo", "bar"]),
    eq(["foo", "bar"], ["foo", "baz"]),
]
>> [true, false]
```

```
# Equality of nested arrays
[
    eq([[1, 2], [3, 4]], [[1, 2], [3, 4]]),
    eq([[1, 2], [3, 4]], [[1, 2], [3, 5]]),
]
>> [true, false]
```

```
# Equality of objects
[
    eq(
        {foo: "bar", spam: "eggs"},
        {spam: "eggs", foo: "bar"},
    ),
    eq(
        {foo: "bar", spam: "eggs"},
        {spam: "eggs", foo: "baz"},
    ),
]
>> [true, false]
```

```
# Equality of different types
// Values of different types are never equal
[eq(null, false), eq(1, true), eq(42, "42")]
>> [false, false, false]
```

### lt|lt

Returns whether its first argument is less than its second argument, according to the [Comparison Rules](#comparison-rules).

Parameters:

- `a` (_Number, String, Boolean, or Array_): The first value.
- `b` (_Number, String, Boolean, or Array_): The second value.

Returns:

- (_Boolean_): Whether `a` is less than `b`.

```
# Less than on booleans
[
    false | lt(true),
    true | lt(true),
    true | lt(false),
]
>> [true, false, false]
```

```
# Less than on numbers
[
    42 | lt(43),
    43 | lt(43),
    43 | lt(42),
]
>> [true, false, false]
```

```
# Less than on strings
[
    "bar" | lt("baz"),
    "baz" | lt("baz"),
    "baz" | lt("bar"),
]
>> [true, false, false]
```

```
# Less than on arrays
[
    ["foo", "bar"] | lt(["foo", "baz"]),
    ["foo", "bar"] | lt(["foo!", "aar"]),
    ["foo", "bar"] | lt(["foo", "bar"]),
    ["foo", "baz"] | lt(["foo", "bar"]),
    ["foo!", "aar"] | lt(["foo", "bar"]),
]
>> [true, true, false, false, false]
```

```
# Less than on nested arrays
[
    [[1, 2], [3, 4]] | lt([[1, 2], [3, 10]]),
    [[1, 2], [3, 4]] | lt([[1, 3], [3, 3]]),
    [[1, 2], [3, 4]] | lt([[1, 2], [3, 4]]),
    [[1, 2], [3, 10]] | lt([[1, 2], [3, 4]]),
    [[1, 3], [3, 3]] | lt([[1, 2], [3, 4]]),
]
>> [true, true, false, false, false]
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

Returns the least element in the specified collection, i.e. the element that is less than or equal to all other elements.

If `by` is `null`, the elements are compared in their natural order, following the [Comparison Rules](#comparison-rules).

Otherwise, `by` is called on each element to obtain a comparison key, and this key is used to determine the least element, again following the Comparison Rules. If multiple elements have the same minimum key, the first one is returned.

If the collection is empty and `default` is provided, the result of calling `default` is returned. Otherwise, an error is thrown.

Parameters:

- `collection` (_Collection_): The values to find the least element of.
- `by` (_Function or Null_, default `null`): A function to produce a comparison key from each element, or `null` to compare the elements in their natural order.
- `default:` (_Function or Null_, default `null`): A function to call and return the result of if the collection is empty, or `null` to throw an error if the collection is empty.

Returns:

- (_Any_): The least element.

```
# Least by natural order
// Elements are compared in their natural order by default
[97, 42, 216] | least
>> 42
```

```
# Least by comparison key
// Elements can be compared by a comparison key
["foobar", "zoo", "spam"]
| least(by: length)
>> "zoo"
```

```
# Least with equal keys
// If elements are tied, the first one is returned
["spam", "foo", "bar", "eggs"]
| least(by: length)
>> "foo"
```

```
# Least with default
// A default value can be provided for empty collections
[
    [] | least(default: $ 42),
    ["foo"] | least(default: $ 42),
]
>> [42, "foo"]
```

### greatest|greatest

Returns the greatest element in the specified collection, i.e. the element that is greater than or equal to all other elements.

If `by` is `null`, the elements are compared in their natural order, following the [Comparison Rules](#comparison-rules).

Otherwise, `by` is called on each element to obtain a comparison key, and this key is used to determine the greatest element, again following the Comparison Rules. If multiple elements have the same maximum key, the first one is returned.

If the collection is empty and `default` is provided, the result of calling `default` is returned. Otherwise, an error is thrown.

Parameters:

- `collection` (_Collection_): The values to find the greatest element of.
- `by` (_Function or Null_, default `null`): A function to produce a comparison key from each element, or `null` to compare the elements in their natural order.
- `default:` (_Function or Null_, default `null`): A function to call and return the result of if the collection is empty, or `null` to throw an error if the collection is empty.

Returns:

- (_Number, String, Boolean, or Array_): The greatest element.

```
# Greatest by natural order
// Elements are compared in their natural order by default
[97, 42, 216] | greatest
>> 216
```

```
# Greatest by comparison key
// Elements can be compared by a comparison key
["foobar", "zoo", "spam"]
| greatest(by: length)
>> "foobar"
```

```
# Greatest with equal keys
// If elements are tied, the first one is returned
["spam", "foo", "bar", "eggs"]
| greatest(by: length)
>> "spam"
```

```
# Greatest with default
// A default value can be provided for empty collections
[
    [] | greatest(default: $ 42),
    ["foo"] | greatest(default: $ 42),
]
>> [42, "foo"]
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
// These second conditions would cause errors if evaluated
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
// These second conditions would cause errors if evaluated
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

- `Collection` covers values that contain other values.
- `Sequence` covers collections with a definite order, such as strings, arrays, and streams.
- `Display` covers instances that customize how the `display` function treats them.
- `Instance` covers all values except null, booleans, numbers, strings, arrays, objects, and functions.
- `Type` covers values that represent Kenpali types: classes and protocols.
- `Any` covers all values.

```
# Protocol constants
[
    Collection,
    Sequence,
    Display,
    Instance,
    Type,
    Any,
]
| transform(|.name)
| toArray
>> ["Collection", "Sequence", "Display", "Instance", "Type", "Any"]
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
    isNull(42),
]
>> [true, false]
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
    isBoolean(false),
    isBoolean(true),
    isBoolean(42),
]
>> [true, true, false]
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
    isNumber(42),
    isNumber("foo"),
]
>> [true, false]
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
    isString("foo"),
    isString(42),
]
>> [true, false]
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
// Streams display only elements that have already been evaluated
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
// A function displays its name and the names of its enclosing functions
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
    isArray([1, 2, 3]),
    isArray(42),
]
>> [true, false]
```

### toArray|toArray

Dumps the elements of the specified collection into an array.

Parameters:

- `value` (_Collection_): The value to convert.

Returns:

- (_Array_): The array of elements.

```
# To array
[
    [42, 97, 216] | toArray,
    1 | to(5) | toArray,
    [42, 97, 216] | newSet | toArray | sort,
]
>> [[42, 97, 216], [1, 2, 3, 4, 5], [42, 97, 216]]
```

### isStream|isStream

Returns whether its argument is a stream.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a stream.

```
# Is stream
[
    isStream(1 | to(3)),
    isStream(42),
]
>> [true, false]
```

### toStream|toStream

Returns a stream that iterates over the elements of the specified collection. If the collection is not a sequence, the order in which the stream returns the elements may not be predictable.

If the argument is already a stream, the same stream is returned, rather than a wrapper, i.e. `x | toStream | eq(x)` is true if `x` is a stream.

Parameters:

- `value` (_Collection_): The value to convert.

Returns:

- (_Stream_): The stream of elements.

```
# To stream
[
    [42, 97, 216] | toStream | isStream,
    [42, 97, 216] | newSet | toStream | isStream,
]
>> [true, true]
```

```
# To stream on stream
// When called on a stream, the same stream is returned
stream = 1 | to(5);
stream | toStream | eq(stream)
>> true
```

### isObject|isObject

Returns whether its argument is an object.

Note that instances are _not_ objects.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is an object.

```
# Is object
[
    isObject({foo: 1, bar: 2}),
    isObject(42),
    isObject(newSet()),
]
>> [true, false, false]
```

### toObject|toObject

Converts the specified value into an object.

If the value is a sequence, its elements are treated as key-value pairs. Any duplicate keys are assigned the value from the _last_ pair with that key in the sequence.

If the value is a non-sequence instance, an object containing its class name and properties is returned.

Parameters:

- `value` (_Sequence or Instance_): The value to convert. If a sequence, it must contain tuples like _[string, any]_.

Returns:

- (_Object_): An object constructed from `value`.

```
# Object from properties
// Arrays are treated as key-value pairs
properties = [["foo", "bar"], ["spam", "eggs"]];
properties | toObject
>> {foo: "bar", spam: "eggs"}
```

```
# Object from properties with duplicates
// Properties overwrite earlier duplicates
properties = [["foo", "bar"], ["spam", "eggs"], ["foo", "baz"]];
properties | toObject
>> {foo: "baz", spam: "eggs"}
```

```
# Object from instance
// Properties are extracted from instances
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
    isFunction((x) => x),
    isFunction(42),
]
>> [true, false]
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
    isError(newError("badIdea")),
    isError(42),
]
>> [true, false]
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
    isClass(Number),
    isClass(42),
]
>> [true, false]
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
    isProtocol(Sequence),
    isProtocol(42),
]
>> [true, false]
```

### isSequence|isSequence

Returns whether its argument is a sequence.

Parameters:

- `value` (_Any_): The value to check.

Returns:

- (_Boolean_): Whether `value` is a sequence.

```
# Is sequence
[
    isSequence([1, 2, 3]),
    isSequence(1 | to(3)),
    isSequence(42),
]
>> [true, true, false]
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
    isType(Number),
    isType(Sequence),
    isType(42),
]
>> [true, true, false]
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
    isInstance(1 | to(3)),
    isInstance(newError("badIdea")),
    isInstance(Number),
    isInstance(Sequence),
    isInstance(42),
]
>> [true, true, true, true, false]
```

## Control Flow|control-flow

### if|if

Evaluates and returns the result of one of two functions based on a condition.

Only the function matching the condition is evaluated.

The `else` branch can be omitted, useful if the `then` branch only runs side effects, and there's nothing to do if the condition is false.

Parameters:

- `condition` (_Boolean_): The condition to check.
- `then:` (_Function_): The function to call if `condition` is `true`.
- `else:` (_Function or Null_, default `null`): The function to call if `condition` is `false`, or `null` to return `null` if `condition` is `false`.

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
// These branches would cause errors if evaluated
[
    if(true, then: $ 1, else: $ [] @ 1),
    if(true, then: $ 1, else: $ (foo = foo; foo)),
    if(false, then: $ [] @ 1, else: $ 2),
    if(false, then: $ (foo = foo; foo), else: $ 2),
]
>> [1, 1, 2, 2]
```

```
# If with then only
// The else branch can be omitted if there's nothing to do
foo = (x) => (
    arr = newMutableArray();
    arr.append(if(x | gt(0), then: $ (arr.append(x); "added")));
    arr.elements()
);
[
    foo(42),
    foo(-1),
]
>> [[42, "added"], [null]]
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

### swapIf|swapIf

Passes two arguments to a function, swapping them if a condition is true.

Parameters:

- `values` (_Array_): A two-element tuple of values to pass to the function.
- `condition` (_Boolean or Function_): The condition to check. If this is a function, it is called with the two `values` as its arguments, in order, to get the condition's truth value.
- `f` (_Function_): The function to call with the two `values`. If the condition is true, `f(values @ 2, values @ 1)` is called; otherwise, `f(values @ 1, values @ 2)` is called.

Returns:

- (_Any_): The result of the call to `f`.

```
# Swap if
s = | swapIf((a, b) => a | lt(b), (a, b) => [a | display, b | display] | join);
[
    [42, 73] | s,
    [97, 73] | s,
]
>> ["7342", "9773"]
```

### switch|switch

Evaluates a list of conditions on an input value and returns the result of the first matching case.

Each condition is a `[condition, result]` pair. The `condition` can either be a predicate function to call on `value`, or a value to compare `value` to using the Comparison Rules. If the condition is true, the corresponding result function is called with `value` and its result is returned. Otherwise, the next condition is tried.

If all conditions fail, the `else` function is called with `value` and its result is returned.

Parameters:

- `value` (_Any_): The value to pass to conditions and results.
- `conditions` (_Array of tuple like [Any, Function]_): The conditions to try.
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

```
# Switch with equality shortcut
// `x` can be used as a shortcut for `| eq(x)`
foo = | switch(
    [42, $ "the answer"],
    [97, $ "the question"],
    else: (x) => ["an ordinary number, ", x | display] | join,
);
[
    foo(42),
    foo(97),
    foo(1),
]
>> ["the answer", "the question", "an ordinary number, 1"]
```

## Stream Builders|stream-builders

These functions build up new streams from scalar inputs.

### Stream|stream

A _stream_ is a lazily computed sequence of values. It is inspired by similar concepts in other languages (often called _generators_ or _iterators_), but the key difference is that streams _remember_ the values they have already computed, so they can be iterated over multiple times. Of course, this should be implemented in such a way that values that are _no longer reachable_ are freed—for example, by storing them as a singly-linked list.

In general, streams must not compute anything more than is absolutely necessary to produce the values that have been requested. They must also take care to avoid stack overflow—iterating over a stream must interleave _calls_ and _returns_ in a way that avoids building up a large call stack.

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

```
# Ranges with wrong-way steps
[
    1 | to(10, by: -3) | toArray,
    5 | to(1, by: 1) | toArray,
]
>> [[], []]
```

### toSize|toSize

Generates a stream of evenly spaced numbers with a given size.

Parameters:

- `start` (_Number_): The number to start counting from.
- `size` (_Number_): The number of elements to include in the stream.
- `by:` (_Number_, default `1`): The number to count by. Each element of the stream will be `by` more than the previous one. If `by` is negative, the stream will count down.

Returns:

- (_Stream_): A stream of numbers counting up from `start` incrementing by `by`, with `size` elements in total.

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
# Ranges defined by size with step
[
    1 | toSize(4, by: 3) | toArray,
    5 | toSize(3, by: 2) | toArray,
]
>> [
    [1, 4, 7, 10],
    [5, 7, 9],
]
```

```
# Ranges defined by size with negative step
[
    5 | toSize(5, by: -1) | toArray,
    10 | toSize(3, by: -2) | toArray,
]
>> [
    [5, 4, 3, 2, 1],
    [10, 8, 6],
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
- `default:` (_Function or Null_, default `null`): A function to call and return the result of if the sequence is empty, or `null` to throw an error if the sequence is empty.

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

```
# Last element with default
[
    [] | last(default: $ 42),
    ["foo"] | last(default: $ 42),
]
>> [42, "foo"]
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

If `n` is less than one, the resulting sequence is empty.

Parameters:

- `sequence` (_Sequence_): The sequence to take values from.
- `n` (_Number_): The number of elements to keep.

Returns:

- (_Array or String_): An array of at most `n` elements, or a string containing the last `n` characters if `sequence` is a string.

```
# Keeping trailing elements
[
    "foobar" | keepLast(3),
    "foobar" | keepLast(0),
    "foobar" | keepLast(-1),
    [42, 97, 6, 12, 64] | keepLast(3),
    [42, 97, 6, 12, 64] | keepLast(0),
    [42, 97, 6, 12, 64] | keepLast(-1),
]
>> [
    "bar",
    "",
    "",
    [6, 12, 64],
    [],
    [],
]
```

### dropLast|dropLast

Drops the last `n` elements of the input.

If `n` is less than one, the sequence is returned unchanged.

Parameters:

- `sequence` (_Sequence_): The sequence to drop values from.
- `n` (_Number_, default: `1`): The number of elements to drop.

Returns:

- (_Array or String_): An array with the last `n` elements dropped, or a string with the last `n` characters dropped if `sequence` is a string.

```
# Dropping trailing elements
arr = [42, 97, 6, 12, 64];
[
    "foobar" | dropLast,
    "foobar" | dropLast(2),
    "foobar" | dropLast(0),
    "foobar" | dropLast(-1),
    arr | dropLast,
    arr | dropLast(3),
    arr | dropLast(0),
    arr | dropLast(-1),
]
>> [
    "fooba",
    "foob",
    "foobar",
    "foobar",
    [42, 97, 6, 12],
    [42, 97],
    [42, 97, 6, 12, 64],
    [42, 97, 6, 12, 64],
]
```

### count|count

Returns the number of elements in the collection matching the specified condition.

Parameters:

- `collection` (_Collection_): The collection to count from.
- `condition` (_Function_): A function that returns `true` for elements to count.

Returns:

- (_Number_): The number of elements for which `condition` returns `true`.

```
# Counting
[1, 10, 2, 9, 3, 12] | count(| lt(10))
>> 4
```

### forAll|forAll

Tests whether the specified condition is true for all elements in the collection.

Parameters:

- `collection` (_Collection_): The collection to test.
- `condition` (_Function_): A function that returns `true` for acceptable elements.

Returns:

- (_Boolean_): Whether all elements match the condition.

```
# True for all elements
[
    [1, 2, 3] | forAll(| lt(10)),
    [1, 42, 3] | forAll(| lt(10)),
]
>> [true, false]
```

### forSome|forSome

Tests whether the specified condition is true for at least one element in the collection.

Parameters:

- `collection` (_Collection_): The collection to test.
- `condition` (_Function_): A function that returns `true` for acceptable elements.

Returns:

- (_Boolean_): Whether any elements match the condition.

```
# True for some elements
[
    [41, 2, 43] | forSome(| lt(10)),
    [41, 42, 43] | forSome(| lt(10)),
]
>> [true, false]
```

### reverse|reverse

Returns an array containing all the elements of the specified sequence in reverse order.

Parameters:

- `sequence` (_Sequence_): The sequence to reverse.

Returns:

- (_Array_): The reversed array.

```
# Reversing
["foo", "bar", "baz"] | reverse
>> ["baz", "bar", "foo"]
```

### sort|sort

Returns an array with the same elements as the specified collection, but in ascending order.

If `by` is `null`, the elements are sorted in their natural order, following the [Comparison Rules](#comparison-rules).

Otherwise, `by` is called on each element to obtain a sort key, and the elements are sorted by that sort key, again following the Comparison Rules. The `by` function is only called once for each element.

The sort is stable: if the collection is a sequence, two elements with equal sort keys will appear in the same relative order in the sorted array as they were in the original sequence.

Parameters:

- `collection` (_Collection_): The collection to sort.
- `by:` (_Function or Null_, default `null`): A function to produce a sort key from each element, or `null` to sort the elements in their natural order.

Returns:

- (_Array_): The sorted array.

```
# Sorting in natural order
// Elements are sorted in their natural order by default
["foo", "bar", "spam", "eggs"] | sort
>> ["bar", "eggs", "foo", "spam"]
```

```
# Sorting arrays lexicographically
// Arrays are sorted lexicographically by their elements
[[10, 2], [2, 1], [11, 3]] | sort
>> [[2, 1], [10, 2], [11, 3]]
```

```
# Sorting by sort key
// Elements can be sorted by a sort key
["foobar", "zoo", "spam"]
| sort(by: (word) => word | length)
>> ["zoo", "spam", "foobar"]
```

```
# Sort stability
// Tied elements remain in their original order
["spam", "foo", "bar", "eggs"]
| sort(by: (word) => word | length)
>> ["foo", "bar", "spam", "eggs"]
```

### group|group

Groups a collection of pairs by their first element.

Parameters:

- `pairs` (_Collection_): A collection of pairs whose first element is the grouping key.
- `onGroup:` (_Function_, default `(x) => x`): A function to call on each group after assembling it.

Returns:

- (_Array_): An array of pairs whose first element is the grouping key, and whose second element is the result of calling `onGroup` on an array of the second elements of the input pairs with that grouping key.

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

Groups a collection by a key function.

Parameters:

- `collection` (_Collection_): The collection to group.
- `by` (_Function_): A function that returns a grouping key for a given value.
- `onGroup:` (_Function_, default `(x) => x`): A function to call on each group after assembling it.

Returns:

- (_Array_): An array of pairs whose first element is the grouping key, and whose second element is the result of calling `onGroup` on an array of the elements with that grouping key.

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

Applies a function to each element in a collection for its side effects.

Parameters:

- `collection` (_Collection_): The collection of values to iterate over.
- `action` (_Function_): The function to apply to each element.

Returns:

- (_Array_): An array containing the elements of the input collection.

```
# Applying a side effect to each element
result = newMutableArray();
["foo", "bar", "baz"] | forEach(result.append);
1 | to(5) | forEach(result.append);
result.elements()
>> ["foo", "bar", "baz", 1, 2, 3, 4, 5]
```

### transformArray|transformArray

Does `transform` followed by `toArray`.

Parameters:

- `collection` (_Collection_): The collection to transform.
- `f` (_Function_): The function to apply to each element.

Returns:

- (_Array_): An array of the transformed values.

```
# Transform array
[1, 2, 3] | transformArray((i) => mul(i, i))
>> [1, 4, 9]
```

## Stream Accessors|stream-accessors

These functions calculate a scalar value from a stream, but only access a finite number of elements to do so. Therefore, they are safe to call even on infinite streams.

### isEmpty|isEmpty

Tests whether the specified collection has no elements.

Parameters:

- `collection` (_Collection_): The collection to check for emptiness.

Returns:

- (_Boolean_): Whether the collection is empty.

```
# Is empty
[
    "" | isEmpty,
    "foo" | isEmpty,
    [] | isEmpty,
    [1] | isEmpty,
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
- `default:` (_Function or Null_, default `null`): A function to call and return the result of if the sequence is empty, or `null` to throw an error if the sequence is empty.

Returns:

- (_Any_): The first element.

```
# First element
[
    ["foo"] | first,
    ["foo", "bar", "baz"] | first
]
>> ["foo", "foo"]
```

```
# First element with default
[
    [] | first(default: $ 42),
    ["foo"] | first(default: $ 42),
]
>> [42, "foo"]
```

## Stream Rebuilders|stream-rebuilders

These functions create new streams that depend on existing ones, preserving stream laziness.

### transform|transform

Creates a stream from applying a function to each element in an input collection.

Parameters:

- `collection` (_Collection_): The collection of values to transform.
- `f` (_Function_): The function to apply to each element.

Returns:

- (_Stream_): A stream of transformed values.

```
# Transforming
[1, 2, 3] | transform((i) => mul(i, i)) | toArray
>> [1, 4, 9]
```

### running|running

Maintains state while processing a sequence, producing a stream of intermediate states.

Parameters:

- `in` (_Sequence_): The sequence of values to process.
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

### with|with

Tags each element of the input sequence with the result of calling a function on it.

Parameters:

- `sequence` (_Sequence_): The sequence to add tags to.
- `f` (_Function_): The function to call on each element.

Returns:

- (_Stream_): A stream of pairs, where the first element is the tag and the second element is the element from the input sequence.

```
# Adding tags
["fooo", "fo", "foo"] | with(length) | toArray
>> [[4, "fooo"], [2, "fo"], [3, "foo"]]
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

### keepFirst|keepFirst

Retains only the first `n` elements of the input.

If `n` is less than one, the resulting sequence is empty.

Parameters:

- `sequence` (_Sequence_): The sequence to take values from.
- `n` (_Number_): The number of elements to keep.

Returns:

- (_Stream or String_): A stream of at most `n` elements, or a string containing the first `n` characters if `sequence` is a string.

```
# Keeping leading elements
[
    "foobar" | keepFirst(3),
    "foobar" | keepFirst(0),
    "foobar" | keepFirst(-1),
    [42, 97, 6, 12, 64] | keepFirst(3) | toArray,
    [42, 97, 6, 12, 64] | keepFirst(0) | toArray,
    [42, 97, 6, 12, 64] | keepFirst(-1) | toArray,
]
>> [
    "foo",
    "",
    "",
    [42, 97, 6],
    [],
    [],
]
```

### dropFirst|dropFirst

Skips the first `n` elements of the input.

If `n` is less than one, the sequence is returned unchanged.

Parameters:

- `sequence` (_Sequence_): The sequence to skip values from.
- `n` (_Number_, default: `1`): The number of elements to drop.

Returns:

- (_Stream or String_): A stream with the first `n` elements skipped, or a string with the first `n` characters skipped if `sequence` is a string.

```
# Dropping leading elements
arr = [42, 97, 6, 12, 64];
[
    "foobar" | dropFirst,
    "foobar" | dropFirst(2),
    "foobar" | dropFirst(0),
    "foobar" | dropFirst(-1),
    arr | dropFirst | toArray,
    arr | dropFirst(3) | toArray,
    arr | dropFirst(0) | toArray,
    arr | dropFirst(-1) | toArray,
]
>> [
    "oobar",
    "obar",
    "foobar",
    "foobar",
    [97, 6, 12, 64],
    [12, 64],
    [42, 97, 6, 12, 64],
    [42, 97, 6, 12, 64],
]
```

### slice|slice

Extracts a sub-sequence of the specified sequence.

Negative indices count from the end of the sequence, as with [`at`](#at).

Indices are allowed to be out of bounds, in which case the slice goes as far as it can: a slice from zero starts at the beginning of the sequence, and a slice to a number greater than the length goes to the end of the sequence.

If `from` comes after `to`, the slice is empty.

Parameters:

- `sequence` (_Sequence_): The sequence to take values from.
- `from:` (_Number_): The index of the first element to take.
- `to:` (_Number_): The index of the last element to take.

Returns:

- (_Stream or String_): A stream containing only the elements from index `from` to index `to`, or a string with the characters at those indices if `sequence` is a string.

```
# Slicing
arr = [42, 97, 6, 12, 64, 73];
[
    "foobar" | slice(from: 2, to: 4),
    "foobar" | slice(from: 2, to: 10),
    "foobar" | slice(from: 0, to: 4),
    "foobar" | slice(from: 2, to: -2),
    "foobar" | slice(from: -4, to: 4),
    "foobar" | slice(from: -4, to: -2),
    "foobar" | slice(from: 4, to: 2),
    arr | slice(from: 2, to: 4) | toArray,
    arr | slice(from: 2, to: 10) | toArray,
    arr | slice(from: 0, to: 4) | toArray,
    arr | slice(from: 2, to: -2) | toArray,
    arr | slice(from: -4, to: 4) | toArray,
    arr | slice(from: -4, to: -2) | toArray,
    arr | slice(from: 4, to: 2) | toArray,
]
>> [
    "oob",
    "oobar",
    "foob",
    "ooba",
    "ob",
    "oba",
    "",
    [97, 6, 12],
    [97, 6, 12, 64, 73],
    [42, 97, 6, 12],
    [97, 6, 12, 64],
    [6, 12],
    [6, 12, 64],
    [],
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

### dropWhile|dropWhile

Creates a stream of elements from the input sequence, skipping elements while a condition holds.

This skips exactly the elements that `while` keeps. It returns an empty stream if the condition is never true.

Parameters:

- `sequence` (_Sequence_): The sequence to process.
- `condition` (_Function returning Boolean_): A function that returns `true` for elements to skip.

Returns:

- (_Stream_): A stream containing the elements the first element for which `condition` returns `false`, and all elements after it.

```
# Drop while
1
| build(| mul(2))
| dropWhile(| lt(100))
| keepFirst(3)
| toArray
>> [128, 256, 512]
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

### where|where

Filters a collection, keeping only elements that satisfy a condition.

Parameters:

- `collection` (_Collection_): The collection to filter.
- `condition` (_Function_): A function that returns `true` for elements to keep.

Returns:

- (_Stream_): A stream of elements where `condition` returns `true`.

```
# Filtering
[1, 10, 2, 9, 3, 12] | where(| lt(10)) | toArray
>> [1, 2, 9, 3]
```

### distinct|distinct

Keeps only the unique elements in a collection.

Elements are considered equal based on the [Comparison Rules](#comparison-rules).

If the argument is a sequence, the resulting stream is in the order each element first appears in the sequence.

Parameters:

- `collection` (_Collection_): The collection to remove duplicates from.

Returns:

- (_Stream_): A stream of the unique elements in the collection.

```
# Distinct
[42, 97, 42, 73] | distinct | toArray
>> [42, 97, 73]
```

### zip|zip

Combines multiple sequences into a stream of tuples, stopping when the shortest sequence is exhausted.

To force `zip` to continue past the end of one of the sequences, extend that sequence with `thenRepeat`, passing the desired default value.

Parameters:

- `*sequences` (_Array of Sequence_): The sequences to combine.

Returns:

- (_Stream of Array_): A stream of tuples, where each tuple contains corresponding elements from the input sequences.


```
# Zipping
[1, 2, 3] | zip(["one", "two", "three"]) | toArray
>> [[1, "one"], [2, "two"], [3, "three"]]
```

```
# Zip with uneven sequences
// Zip stops when the shortest sequence is exhausted
[1, 2, 3] | zip(["one", "two"]) | toArray
>> [[1, "one"], [2, "two"]]
```

```
# Zip continuing past the end of a sequence
// thenRepeat can be used to prevent stopping
[1, 2, 3] | zip(["one", "two"] | thenRepeat(null)) | toArray
>> [[1, "one"], [2, "two"], [3, null]]
```

```
# Zip with more than two sequences
// Zip can combine more than two sequences
[1, 2, 3] | zip(["one", "two", "three"], ["un", "deux", "trois"]) | toArray
>> [[1, "one", "un"], [2, "two", "deux"], [3, "three", "trois"]]
```

### unzip|unzip

Splits a sequence of tuples into multiple streams.

While `unzip` does essentially the same transformation as `zip` (transposing a nested sequence), it emits the results in a different format (an array of streams instead of a stream of arrays), which makes it useful in different situations.

Note that `unzip` does not attempt to guess how many streams to produce. By default, it always produces two streams, ignoring any additional elements in the input tuples. Use the `numStreams` parameter if you need a different number of streams.

Parameters:

- `sequence` (_Sequence of Array_): A sequence where each element is a tuple.
- `numStreams:` (_Number_, default: `2`): The number of streams to produce.

Returns:

- (_Array of Stream_): An array of `numStreams` streams, where the `i`-th stream contains the `i`-th element from each tuple.

```
# Unzipping
[[1, "one"], [2, "two"], [3, "three"]]
| unzip
| transform(| toArray)
| toArray
>> [[1, 2, 3], ["one", "two", "three"]]
```

```
# Unzipping with more than two streams
[[1, "one", "eins"], [2, "two", "zwei"], [3, "three", "drei"]]
| unzip(numStreams: 3)
| transform(| toArray)
| toArray
>> [[1, 2, 3], ["one", "two", "three"], ["eins", "zwei", "drei"]]
```

### flatten|flatten

Flattens a sequence of sequences into a single stream.

Only _one_ level of nesting is flattened.

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
]
>> [
    [],
    [],
    [1, 2, 3, 4, 5, [6]],
    [1, 2, 3],
]
```

### transformFlat|transformFlat

Does `transform` followed by `flatten`.

Parameters:

- `sequence` (_Sequence_): The sequence to process.
- `f` (_Function returning Sequence_): The function to apply to each element.

Returns:

- (_Stream_): A stream containing the flattened results of applying `f` to each element of the input sequence.

```
# Transform flat
[1, 2, 3] | transformFlat((x) => [x, x | mul(x)]) | toArray
>> [1, 1, 2, 4, 3, 9]
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
]
>> [
    [],
    [[2, 8], [9], [3, 7]],
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
]
>> [
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11]],
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

Returns the properties of an object or instance, as an array of name-value pairs.

Parameters:

- `object` (_Object or Instance_): The object or instance whose properties to retrieve.

Returns:

- (_Array_): An array of the properties.

```
# Array of properties of an object
{foo: 1, bar: 2} | properties
>> [["foo", 1], ["bar", 2]]
```

```
# Array of properties of an instance
newError("badIdea", foo: "bar") | properties
>> [["type", "badIdea"], ["details", {foo: "bar"}], ["calls", []]]
```

### merge|merge

Combines the properties of the specified objects into a new object.

If the same key appears in more than one input object, the resulting object takes the property value from the last such object.

Parameters:

- `objects` (_Sequence of Object_): The objects to combine.

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

- `collection` (_Sequence or Object or Instance_): The collection to index into.
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
]
>> [2, 3, 5, 3, 4, 4, 42, 42, 42]
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

Objects can be indexed with string keys.

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

Instances can also be indexed with string keys.

```
# Indexing instances
instance = newError("badIdea", foo: "bar");
[
    instance | at("type"),
    instance | at("details", default: $ {}),
    instance | at("nonExistentProperty", default: $ []),
]
>> ["badIdea", {foo: "bar"}, []]
```

## Utilities|utilities

### write|write

Writes the specified message to a diagnostic log. Exactly where this goes depends on the implementation and configuration; writing to standard error is a common default.

Parameters:

- `message` (_String_): The message to write.

Returns:

- The value `null`.

```
# Writing a message
write("Hello, world!") // Open the console to see the message!
>> null
```

### debug|debug

Writes a representation of the specified value to the diagnostic log (the same place as `write`).

This function calls `display` on the value before writing it to the log. In particular, this means that strings are quoted; to write the _contents_ of a string, use `write` instead.

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

### cache|cache

Creates a function that remembers its result for each combination of arguments it has been called with.

Parameters:

- `f` (_Function_): A function wrap with a cache.

Returns:

- (_Function_): The wrapped function.

```
# Caching
out = newMutableArray();
foo = cache(| mul(2) | also(| out.append));
[foo(42), foo(73), foo(42), out.elements()]
>> [84, 146, 84, [84, 146]]
```

```
# Caching with multiple arguments
out = newMutableArray();
foo = cache((a, b:) => [a, b] | join | also(| out.append));
[foo("bar", b: "baz"), foo("bar", b: "qux"), foo("bar", b: "baz"), out.elements()]
>> ["barbaz", "barqux", "barbaz", ["barbaz", "barqux"]]
```

## Sets and Maps|sets-maps

### Set|Set

A `Set` is like an array, but it can't have duplicate elements, and checking whether an element is in the set is fast regardless of the size of the set.

#### newSet|newSet

Creates an immutable set.

Parameters:

- `elements` (_Collection_, default `[]`): The set's elements. Duplicate elements are silently discarded.

Returns:

- (_Set_): The new set.

```
# Set creation
[42, 97, 42, 73] | newSet | sort
>> [42, 73, 97]
```

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
# Set methods
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

### Map|Map

A `Map` is a collection of key-value pairs, where keys are unique. Looking up a value by key is fast regardless of the size of the map.

#### newMap|newMap

Creates an immutable map.

Parameters:

- `entries` (_Collection_, default `[]`): A collection of `[key, value]` pairs. If duplicate keys exist, only the last one is kept.

Returns:

- (_Map_): The new map.

```
# Map creation
[["foo", 42], ["bar", 73], ["bar", 97]] | newMap | sort
>> [["bar", 97], ["foo", 42]]
```

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

- `elements` (_Sequence_, default `[]`): The initial elements of the array.

Returns:

- (_MutableArray_): The new mutable array.

```
# Mutable array creation
[42, 97, 42, 73] | newMutableArray |.elements()
>> [42, 97, 42, 73]
```

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
# Mutating an array while iterating over it
// Stream functions iterate over a snapshot
array = ["foo", "bar", "baz"] | newMutableArray;
[
    array | transform((element) => (
        array.set(3, [element, element] | join);
        element
    ))
    | toArray,
    array.elements(),
]
>> [["foo", "bar", "baz"], ["foo", "bar", "bazbaz"]]
```

### MutableSet|MutableSet

A mutable set is like a regular set, but it allows adding and removing elements dynamically.

#### newMutableSet|newMutableSet

Creates a mutable set.

Parameters:

- `elements` (_Collection_, default `[]`): The initial elements of the set. Duplicate elements are silently discarded.

Returns:

- (_MutableSet_): The new mutable set.

```
# Mutable set creation
[42, 97, 42, 73] | newMutableSet | sort
>> [42, 73, 97]
```

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
# Mutating a set while iterating over it
set = ["foo", "bar", "baz"] | newMutableSet;
[
    set | transform((element) => (
        set.add("bazbaz");
        element
    ))
    | toArray | sort,
    set.elements() | sort,
]
>> [["bar", "baz", "foo"], ["bar", "baz", "bazbaz", "foo"]]
```

### MutableMap|MutableMap

A mutable map is a collection of key-value pairs where keys are unique, and entries can be added, updated, and removed dynamically.

#### newMutableMap|newMutableMap

Creates a mutable map.

Parameters:

- `entries` (_Collection_, default `[]`): A collection of `[key, value]` pairs to initialize the map.

Returns:

- (_MutableMap_): The new mutable map.

```
# Mutable map creation
[["foo", 42], ["bar", 73], ["bar", 97]] | newMutableMap | sort
>> [["bar", 97], ["foo", 42]]
```

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
# Mutating a map while iterating over it
map = [["foo", 42], ["bar", 97]] | newMutableMap;
[
    map | transform(([key, value]) => (
        map.set("bazbaz", 216);
        [key, value]
    ))
    | toArray | sort,
    map.entries() | sort,
]
>> [[["bar", 97], ["foo", 42]], [["bar", 97], ["bazbaz", 216], ["foo", 42]]]
```

### also|also

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
# Matching against classes
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
    newVar(42) | matches(Error),
    newVar(42) | matches(Var),
    newSet() | matches(Var),
    newSet() | matches(Set),
    null | matches(Set),
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
    true,
    false,
    true,
    false,
]
```

```
# Matching against protocols
[
    "foo" | matches(Collection),
    [1, 2, 3] | matches(Collection),
    {foo: 1, bar: 2} | matches(Collection),
    classOf | matches(Collection),
    ((x) => x) | matches(Collection),
    newError("badIdea") | matches(Collection),
    Number | matches(Collection),
    Sequence | matches(Collection),

    "foo" | matches(Sequence),
    [1, 2, 3] | matches(Sequence),
    {foo: 1, bar: 2} | matches(Sequence),
    classOf | matches(Sequence),
    ((x) => x) | matches(Sequence),
    newError("badIdea") | matches(Sequence),
    Number | matches(Sequence),
    Sequence | matches(Sequence),

    "foo" | matches(Instance),
    [1, 2, 3] | matches(Instance),
    {foo: 1, bar: 2} | matches(Instance),
    classOf | matches(Instance),
    ((x) => x) | matches(Instance),
    newError("badIdea") | matches(Instance),
    Number | matches(Instance),
    Sequence | matches(Instance),

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
    true,
    false,
    false,
    false,
    false,
    false,

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
    true,
    true,
    true,

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
