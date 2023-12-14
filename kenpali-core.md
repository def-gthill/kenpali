# Kenpali Core Specification

The Core functions must be available to any Kenpali program, but they can be implemented on top of the builtins in pure Kenpali. Reference implementations are provided in `core.kpc`.

## Arithmetic

```
# Minus
[minus(5, 2), minus(2, 5)]
>> [3, -3]
```

## Strings

```
# Array of characters in a string
characters("foobar")
>> ["f", "o", "o", "b", "a", "r"]
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

```
# Splitting on a delimiter
[
    "foo,bar,,baz" | split(","),
    "foo=>bar=>=>baz" | split("=>"),
    "foo||bar||||baz" | split("||"),
    "|foo||bar|" | split("|"),
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
# Joining lines
["foo", "bar", "", "baz"] | joinLines
>> "foo\nbar\n\nbaz"
```

```
# Trimming whitespace
[
    "" | trim,
    "  " | trim,
    "foo" | trim,
    "   foo    " | trim,
    "  foo bar baz " | trim,
]
>> ["", "", "foo", "foo", "foo bar baz"]
```

## Comparison

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

## Arrays

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
# Transforming
[1, 2, 3] | forEach((i) => times(i, i))
>> [1, 4, 9]
```

```
# Filtering
[1, 10, 2, 9, 3, 12] | where((i) => (i | isLessThan(10)))
>> [1, 2, 9, 3]
```

```
# Counting
[1, 10, 2, 9, 3, 12] | count((i) => (i | isLessThan(10)))
>> 4
```

```
# Flattening nested arrays
[[1], [2, 3], [4, 5, [6]]] | flatten
>> [1, 2, 3, 4, 5, [6]]
```

## Objects

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
