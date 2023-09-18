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
    [
        42 | isAtMost(43),
        43 | isAtMost(43),
        43 | isAtMost(42),
    ]
]
>> [
    [true, true, false]
]
```

## Arrays

```
# Transforming
[1, 2, 3] | forEach((i) => times(i, i))
>> [1, 4, 9]
```

```
# Flattening nested arrays
[[1], [2, 3], [4, 5, [6]]] | flatten
>> [1, 2, 3, 4, 5, [6]]
```
