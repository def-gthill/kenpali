# Kenpali Core Specification

The Core functions must be available to any Kenpali program, but they can be implemented on top of the builtins in pure Kenpali. Reference implementations are provided in `core.kpc`.

## Arithmetic

```
# Minus
[minus(5, 2), minus(2, 5)]
>> [3, -3]
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
