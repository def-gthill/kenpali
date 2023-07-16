# Kenpali JSON Specification

## Literals

A literal expression has the form `{"literal": <value>}`. The value can be `null`, `true`, `false`, or [any valid JSON string or number](https://www.json.org/json-en.html).

Examples:

```
# Literal null
{"literal": null}
>> null
```

```
# Literal false
{"literal": false}
>> false
```

```
# Literal true
{"literal": true}
>> true
```

```
# Literal number
{"literal": 1}
>> 1
```

```
# Literal string
{"literal": "foobar"}
>> "foobar"
```

## Names

```
# Binding a name
{
    "defining": {"foo": {"literal": 42}},
    "result": {"name": "foo"}
}
>> 42
```

```
# Binding multiple names
{
    "defining": {
        "foo": {"name": "baz"},
        "bar": {"literal": 42},
        "baz": {"name": "bar"}
    },
    "result": {"name": "foo"}
}
>> 42
```

```
# Scope
{
    "defining": {
        "foo": {
            "defining": {
                "bar": {"literal": 42}
            },
            "result": {"literal": null}
        }
    },
    "result": {"name": "bar"}
}
!! nameNotDefined {"name": "bar"}
```

```
# A name from an enclosing scope
{
    "defining": {
        "foo": {"literal": 42}
    },
    "result": {
        "defining": {
            "bar": {"literal": 73}
        },
        "result": {"name": "foo"}
    }
}
>> 42
```

```
# Shadowing
{
    "defining": {
        "foo": {"literal": 42}
    },
    "result": {
        "defining": {
            "foo": {"literal": 73}
        },
        "result": {"name": "foo"}
    }
}
>> 73
```

## Arrays

```
# Array of literals
{"array": [{"literal": 1}, {"literal": 2}, {"literal": 3}]}
>> [1, 2, 3]
```

```
# Array containing an expression to evaluate
{
    "defining": {"foo": {"literal": 42}},
    "result": {"array": [{"name": "foo"}]}
}
>> [42]
```

## Objects

```
# Object with literal values
{
    "object": [
        ["foo", {"literal": "bar"}],
        ["spam", {"literal": "eggs"}]
    ]
}
>> {foo: "bar", spam: "eggs"}
```

```
# Object containing expressions to evaluate
{
    "defining": {"key": {"literal": "foo"}, "value": {"literal": 42}},
    "result": {
        "object": [
            [{"name": "key"}, {"name": "value"}]
        ]
    }
}
>> {foo: 42}
```

## Defining and Calling Functions

```
# One positional parameter, one positional argument
{
    "defining": {"foo": {"given": {"params": ["x"]}, "result": {"name": "x"}}},
    "result": {"calling": {"name": "foo"}, "args": [{"literal": 42}]}
}
>> 42
```

```
# One positional parameter, no arguments
{
    "defining": {"foo": {"given": {"params": ["x"]}, "result": {"name": "x"}}},
    "result": {"calling": {"name": "foo"}}
}
!! missingArgument {"name": "x"}
```

```
# One positional parameter, one optional positional argument
{
    "defining": {"foo": {"given": {"params": ["x"]}, "result": {"name": "x"}}},
    "result": {"calling": {"name": "foo"}, "args": [{"optional": {"literal": 42}}]}
}
>> 42
```

```
# Calling a non-function with no arguments
{
    "calling": {"literal": 42}
}
>> 42
```

```
# Calling a non-function with only optional arguments
{
    "calling": {"literal": 42},
    "args": [{"optional": {"literal": "foo"}}],
    "namedArgs": {"x": {"optional": {"literal": "bar"}}}
}
>> 42
```

A function body can reference names that were in scope when the function was defined, even if those names are out of scope when the function is called.

```
# Closure
{
    "defining": {
        "x": {"literal": 73},
        "foo": {"given": {"params": ["y"]}, "result": {"name": "x"}}
    },
    "result": {"calling": {"name": "foo"}, "args": [{"literal": 42}]}
}
>> 73
```

On the other hand, names that are in scope when the function is called don't leak into the function body.

```
# Leakage
{
    "defining": {
        "leaky": {"given": {"params": ["x"]}, "result": {"name": "intruder"}}
    },
    "result": {
        "defining": {
            "intruder": {"literal": 42}
        },
        "result": {"calling": {"name": "leaky"}, "args": [{"literal": 73}]}
    }
}
!! nameNotDefined {"name": "intruder"}
```

## Quoting and Unquoting

```
# Quoting
{"quote": {"literal": 1}}
>> {literal: 1}
```

```
# Unquoting
{
    "quote": {
        "array": [
            {
                "unquote": {
                    "calling": {"name": "plus"},
                    "args": [{"literal": 1}, {"literal": 1}]
                }
            }
        ]
    }
}
>> {array: [{literal: 2}]}
```

## Errors

```
# Error short-circuiting
{
    "calling": {"name": "negative"},
    "args": [
        {
            "calling": {"name": "plus"},
            "args": [{"literal": "foo"}]
        }
    ]
}
!! wrongArgumentType {"value": "foo", "expectedType": "number"}
```
