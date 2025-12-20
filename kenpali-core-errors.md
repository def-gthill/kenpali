# Kenpali Core Error-Checking Specification

All platform functions must validate their arguments, and return appropriate error values if any arguments are invalid.

- If no argument is supplied for a non-optional parameter, return a `missingArgument` error.
- If an argument of the wrong type is supplied, return a `wrongArgumentType` error.
- If all arguments have the correct types, but the function is undefined for the given values, return an error whose name describes the problem.

## Arithmetic

### add|add

```
# Addition - wrong argument type
add(1, "foo")
!! wrongArgumentType {"value": "foo", "expectedType": "Number"}
```

### negative|negative

```
# Negative - missing argument
negative()
!! missingArgument {"name": "n"}
```

```
# Negative - wrong argument type
negative("foo")
!! wrongArgumentType {"value": "foo", "expectedType": "Number"}
```

## Strings

### join|join

```
# Joining strings - wrong element type
join(["foo", 1])
!! badArgumentValue {"value": ["foo", 1]}
```

## Comparison

### lt|lt

```
# Less than - incomparable types
{} | lt({foo: "bar"})
!! wrongArgumentType {"value": {}, "expectedType": "either(Number, String, Boolean, Array)"}
```

```
# Less than - incompatible types
3 | lt("4")
!! wrongArgumentType {"value": "4", "expectedType": "Number"}
```

```
# Less than - incomparable types in array
[1, 2, {}] | lt([1, 2, 3])
!! wrongArgumentType {"value": {}, "expectedType": "either(Number, String, Boolean, Array)"}
```

```
# Less than - incompatible types in array
[1, 2, 3] | lt([1, 2, "4"])
!! wrongArgumentType {"value": "4", "expectedType": "Number"}
```

### least|least

```
# Least on empty sequence
[] | least
!! indexOutOfBounds {"length": 0, "index": 1}
```

```
# Least with incompatible elements
[97, "42"] | least
!! wrongArgumentType {"value": "42", "expectedType": "Number"}
```

```
# Least with incompatible elements but valid keys
[97, "42"] | least(by: toNumber)
>> "42"
```

```
# Least with incompatible keys
[97, 42] | least(by: (n) => (
    if(n | gt(50), then: $ n, else: $ n | display)
))
!! wrongArgumentType {"value": "42", "expectedType": "Number"}
```

### greatest|greatest

```
# Greatest on empty sequence
[] | greatest
!! indexOutOfBounds {"length": 0, "index": 1}
```

```
# Greatest with incompatible elements
[97, "42"] | greatest
!! wrongArgumentType {"value": "42", "expectedType": "Number"}
```

```
# Greatest with incompatible elements but valid keys
[97, "42"] | greatest(by: toNumber)
>> 97
```

```
# Greatest with incompatible keys
[97, 42] | greatest(by: (n) => (
    if(n | gt(50), then: $ n, else: $ n | display)
))
!! wrongArgumentType {"value": "42", "expectedType": "Number"}
```

## Logic

```
# And - wrong first argument type
and("foo", $ true)
!! wrongArgumentType {"value": "foo", "expectedType": "Boolean"}
```

```
# And - second argument not a function
and(true, true)
!! wrongArgumentType {"value": true, "expectedType": "Function"}
```

```
# And - wrong callback return type
and(true, $ "foo")
!! wrongReturnType {"value": "foo", "expectedType": "Boolean"}
```

```
# Or - wrong first argument type
or("foo", $ false)
!! wrongArgumentType {"value": "foo", "expectedType": "Boolean"}
```

```
# Or - second arugment not a function
or(false, false)
!! wrongArgumentType {"value": false, "expectedType": "Function"}
```

```
# Or - wrong callback return type
or(false, $ "foo")
!! wrongReturnType {"value": "foo", "expectedType": "Boolean"}
```

## Types and Type Conversion

```
# To number - wrong argument type
toNumber([42])
!! wrongArgumentType {"value": [42], "expectedType": "either(String, Number)"}
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

## Stream Collapsers|stream-collapsers

```
# Sorting incompatible elements
[97, "42"] | sort
!! badArgumentValue {"value": [97, "42"]}
```

```
# Sorting incompatible elements with a valid sort key
[97, "42"] | sort(by: toNumber)
>> ["42", 97]
```

```
# Sorting with incompatible sort keys
[97, 42]
| sort(by: (n) => (
    if(n | gt(50), then: $ n, else: $ n | display)
))
!! wrongReturnType {"value": "42", "expectedType": "Number"}
```

## Mutable Values

```
# Mutable array - reading a bad index
["foo", "bar", "baz"] | newMutableArray |.at(4)
!! indexOutOfBounds {"length": 3, "index": 4}
```

```
# Mutable array - reading an index of the wrong type
["foo", "bar", "baz"] | newMutableArray |.at("foo")
!! wrongArgumentType {"value": "foo", "expectedType": "Number"}
```

```
# Mutable array - writing to a bad index
["foo", "bar", "baz"] | newMutableArray |.set(4, "spam")
!! indexOutOfBounds {"length": 3, "index": 4}
```

```
# Mutable array - pop on an empty array
[] | newMutableArray |.pop()
!! indexOutOfBounds {"length": 0, "index": -1}
```

```
# Mutable map - reading a bad key
[["foo", 42], ["bar", 97]] | newMutableMap |.at("spam")
!! missingKey {"key": "\"spam\""}
```

## Validation

```
# Validation - invalid schema
42 | validate({foo: "bar"})
!! invalidSchema {"schema": {"foo": "bar"}}
```

```
# Matches - invalid schema
42 | matches({foo: "bar"})
!! invalidSchema {"schema": {"foo": "bar"}}
```


