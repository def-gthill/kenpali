# Kenpali Core Error-Checking Specification

All builtins must validate their arguments, and return appropriate error values if any arguments are invalid.

- If no argument is supplied for a non-optional parameter, return a `missingArgument` error.
- If an argument of the wrong type is supplied, return a `wrongArgumentType` error.
- If all arguments have the correct types, but the function is undefined for the given values, return an error whose name describes the problem.

## Arithmetic

```
# Plus - wrong argument type
plus(1, "foo")
!! wrongArgumentType {"value": "foo", "expectedType": "number"}
```

```
# Negative - missing argument
negative()
!! missingArgument {"name": "x"}
```

```
# Negative - wrong argument type
negative("foo")
!! wrongArgumentType {"value": "foo", "expectedType": "number"}
```

## Strings

```
# Joining strings - wrong element type
join(["foo", 1])
!! badArgumentValue {"value": ["foo", 1]}
```

## Comparison

```
# Less than - incomparable types
{} | isLessThan(42)
!! wrongArgumentType {"value": {}, "expectedType": {"either": ["number", "string", "boolean", "array"]}}
```

```
# Less than - incompatible types
3 | isLessThan("4")
!! wrongArgumentType {"value": "4", "expectedType": "number"}
```

```
# Less than - incomparable types in array
[1, 2, {}] | isLessThan([1, 2, 3])
!! wrongArgumentType {"value": {}, "expectedType": {"either": ["number", "string", "boolean", "array"]}}
```

```
# Less than - incompatible types in array
[1, 2, 3] | isLessThan([1, 2, "4"])
!! wrongArgumentType {"value": "4", "expectedType": "number"}
```

## Logic

```
# And - wrong first argument type
and("foo", () => true)
!! wrongArgumentType {"value": "foo", "expectedType": "boolean"}
```

```
# And - second argument not a function
and(true, true)
!! wrongArgumentType {"value": true, "expectedType": "function"}
```

```
# And - wrong callback return type
and(true, () => "foo")
!! wrongReturnType {"value": "foo", "expectedType": "boolean"}
```

```
# Or - wrong first argument type
or("foo", () => false)
!! wrongArgumentType {"value": "foo", "expectedType": "boolean"}
```

```
# Or - second arugment not a function
or(false, false)
!! wrongArgumentType {"value": false, "expectedType": "function"}
```

```
# Or - wrong callback return type
or(false, () => "foo")
!! wrongReturnType {"value": "foo", "expectedType": "boolean"}
```

## Types and Type Conversion

```
# To number - wrong argument type
toNumber([42])
!! wrongArgumentType {"value": [42], "expectedType": {"either": ["string", "number"]}}
```

```
# To number - non-numeric string
toNumber("foo")
!! notNumeric {"value": "foo"}
```

```
# To number - string with non-numeric parts
toNumber("42a")
!! notNumeric {"value": "42a"}
```

## Arrays

```
# Non-array returned from build
1 | build(
    while: (n) => n | isLessThan(10),
    out: (n) => n,
    next: (n) => n | increment,
)
!! wrongReturnType {"value": 1, "expectedType": "array"}
```

## Mutable Objects

```
# Mutable array - reading a bad index
["foo", "bar", "baz"] | mutableArray @ at:(4)
!! indexOutOfBounds {"length": 3, "index": 4}
```

```
# Mutable array - writing to a bad index
["foo", "bar", "baz"] | mutableArray @ set:(4, "spam")
!! indexOutOfBounds {"length": 3, "index": 4}
```

```
# Mutable array - pop on an empty array
[] | mutableArray @ pop:()
!! indexOutOfBounds {"length": 0, "index": -1}
```
