# Kenpali Core Error-Checking Specification

All platform functions must validate their arguments, and return appropriate error values if any arguments are invalid.

- If no argument is supplied for a non-optional parameter, return a `missingArgument` error.
- If an argument of the wrong type is supplied, return a `wrongArgumentType` error.
- If all arguments have the correct types, but the function is undefined for the given values, return an error whose name describes the problem.

## Arithmetic

```
# Addition - wrong argument type
add(1, "foo")
!! wrongArgumentType {"value": "foo", "expectedType": "Number"}
```

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

```
# Joining strings - wrong element type
join(["foo", 1])
!! badArgumentValue {"value": ["foo", 1]}
```

## Comparison

```
# Less than - incomparable types
{} | lt(42)
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

## Mutable Objects

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


