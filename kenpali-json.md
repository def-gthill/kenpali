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

## Builtins

```
# Plus
{
    "calling": {"name": "plus"},
    "args": [{"literal": 1}, {"literal": 2}, {"literal": 3}]
}
>> 6
```

```
# Negative
{
    "calling": {"name": "negative"},
    "args": [{"literal": 42}]
}
>> -42
```

```
# Times
{
    "calling": {"name": "times"},
    "args": [{"literal": 2}, {"literal": 3}, {"literal": 4}]
}
>> 24
```

```
# One over
{
    "calling": {"name": "oneOver"},
    "args": [{"literal": 2}]
}
>> 0.5
```

```
# Divide with remainder
{
    "calling": {"name": "divideWithRemainder"},
    "args": [{"literal": 10}, {"literal": 3}]
}
>> {quotient: 3, remainder: 1}
```

```
# Equals on numbers
{
    "array": [
        {
            "calling": {"name": "equals"},
            "args": [{"literal": 42}, {"literal": 42}]
        },
        {
            "calling": {"name": "equals"},
            "args": [{"literal": 42}, {"literal": 43}]
        }
    ]
}
>> [true, false]
```

```
# Equals on arrays
{
    "array": [
        {
            "calling": {"name": "equals"},
            "args": [
                {"array": [{"literal": "foo"}, {"literal": "bar"}]},
                {"array": [{"literal": "foo"}, {"literal": "bar"}]}
            ]
        },
        {
            "calling": {"name": "equals"},
            "args": [
                {"array": [{"literal": "foo"}, {"literal": "bar"}]},
                {"array": [{"literal": "foo"}, {"literal": "baz"}]}
            ]
        }
    ]
}
>> [true, false]
```

```
# Equals on objects
{
    "array": [
        {
            "calling": {"name": "equals"},
            "args": [
                {
                    "object": [
                        ["foo", {"literal": "bar"}],
                        ["spam", {"literal": "eggs"}]
                    ]
                },
                {
                    "object": [
                        ["spam", {"literal": "eggs"}],
                        ["foo", {"literal": "bar"}]
                    ]
                }
            ]
        },
        {
            "calling": {"name": "equals"},
            "args": [
                {
                    "object": [
                        ["foo", {"literal": "bar"}],
                        ["spam", {"literal": "eggs"}]
                    ]
                },
                {
                    "object": [
                        ["spam", {"literal": "eggs"}],
                        ["foo", {"literal": "baz"}]
                    ]
                }
            ]
        }
    ]
}
>> [true, false]
```
