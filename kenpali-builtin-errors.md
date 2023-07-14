# Kenpali Builtin Error-Checking Specification

All builtins must validate their arguments, and return appropriate error values if any arguments are invalid.

- If no argument is supplied for a non-optional parameter, return a `missingArgument` error.
- If a non-optional argument is supplied for a non-existent parameter, return an `unexpectedArgument` error.
- If an argument of the wrong type is supplied, return a `wrongArgumentType` error.
- If all arguments have the correct types, but the function is undefined for the given values, return an error whose name describes the problem.

## Arithmetic

```
# Plus - unexpected argument
plus(foo: 1)
!! unexpectedArgument {"name": "foo"}
```

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
# Negative - unexpected positional argument
negative(1, 2)
!! unexpectedArgument {"position": 2}
```

```
# Negative - unexpected named argument
negative(1, foo: 2)
!! unexpectedArgument {"name": "foo"}
```

```
# Negative - wrong argument type
negative("foo")
!! wrongArgumentType {"value": "foo", "expectedType": "number"}
```
