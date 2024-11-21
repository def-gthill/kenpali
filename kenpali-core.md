# Kenpali Core Specification

The Core functions must be available to any Kenpali program, but they can be implemented on top of the builtins in pure Kenpali. Reference implementations are provided in `core.kpc`.

## Arithmetic

```
# Sum
sum(1 | to(10))
>> 55
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

## Control Flow

```
# But if
[
    42 | butIf((x) => (x | isMoreThan(10)), (x) => (x | minus(5))),
    7 | butIf((x) => (x | isMoreThan(10)), (x) => (x | minus(5))),
]
>> [37, 7]
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
