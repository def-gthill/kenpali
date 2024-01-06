# Kenpali Builtin Error-Checking Specification

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
# Indexing - wrong argument type
42 @ 2
!! wrongArgumentType {"value": 42, "expectedType": {"#either": ["string", "array", "object"]}}
```

```
# Joining - wrong element type
join(["foo", 1])
!! badArgumentValue {"value": ["foo", 1]}
```

## Logic

```
# And - wrong argument type
and(true, "foo")
!! wrongArgumentType {"value": "foo", "expectedType": "boolean"}
```

```
# Or - wrong argument type
or(false, "foo")
!! wrongArgumentType {"value": "foo", "expectedType": "boolean"}
```

## Arrays

```
# Indexing - index less than 1
["foo", "bar"] | at(0)
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 0}
```

```
# Indexing - index greater than length
["foo", "bar"] | at(3)
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 3}
```

## Objects

```
# Indexing - key not present
{"foo": 1} | at("bar")
!! missingProperty {"value": {"foo": 1}, "key": "bar"}
```
