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
    "defining": [["foo", {"literal": 42}]],
    "result": {"name": "foo"}
}
>> 42
```

```
# Binding multiple names
{
    "defining": [
        ["bar", {"literal": 42}],
        ["baz", {"name": "bar"}],
        ["foo", {"name": "baz"}]
    ],
    "result": {"name": "foo"}
}
>> 42
```

```
# Name used before assignment
{
    "defining": [
        ["foo", {"name": "baz"}],
        ["bar", {"literal": 42}],
        ["baz", {"name": "bar"}]
    ],
    "result": {"name": "foo"}
}
!! nameUsedBeforeAssignment {"name": "baz"}
```

```
# Scope
{
    "defining": [
        [
            "foo",
            {
                "defining": [
                    ["bar", {"literal": 42}]
                ],
                "result": {"literal": null}
            }
        ]
    ],
    "result": {"name": "bar"}
}
!! nameNotDefined {"name": "bar"}
```

```
# A name from an enclosing scope
{
    "defining": [
        ["foo", {"literal": 42}]
    ],
    "result": {
        "defining": [
            ["bar", {"literal": 73}]
        ],
        "result": {"name": "foo"}
    }
}
>> 42
```

```
# Shadowing
{
    "defining": [
        ["foo", {"literal": 42}]
    ],
    "result": {
        "defining": [
            ["foo", {"literal": 73}]
        ],
        "result": {"name": "foo"}
    }
}
>> 73
```

```
# Array destructuring
{
    "defining": [
        [
            {"arrayPattern": ["foo", "bar"]},
            {
                "array": [
                    {"literal": 42},
                    {"literal": 97}
                ]
            }
        ]
    ],
    "result": {
        "calling": {"name": "plus"},
        "args": [
            {"name": "foo"},
            {"name": "bar"}
        ]
    }
}
>> 139
```

## Arrays

```
# Empty array
{"array": []}
>> []
```

```
# Array of literals
{"array": [{"literal": 1}, {"literal": 2}, {"literal": 3}]}
>> [1, 2, 3]
```

```
# Array with elements of mixed types
{"array": [{"literal": null}, {"literal": 1}, {"literal": "foo"}]}
>> [null, 1, "foo"]
```

```
# Nested arrays
{"array": [{"array": [{"literal": 1}]}]}
>> [[1]]
```

```
# Array containing an expression to evaluate
{
    "defining": [["foo", {"literal": 42}]],
    "result": {"array": [{"name": "foo"}]}
}
>> [42]
```

```
# Array with spread
{
    "array": [
        {"literal": 42},
        {
            "spread": {
                "array": [
                    {"literal": 1},
                    {"literal": 2},
                    {"literal": 3}
                ]
            }
        },
        {"literal": 97}
    ]
}
>> [42, 1, 2, 3, 97]
```

## Objects

```
# Empty object
{"object": []}
>> {}
```

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
# Object with explicit literal keys
{
    "object": [
        [{"literal": "foo"}, {"literal": "bar"}],
        [{"literal": "spam"}, {"literal": "eggs"}]
    ]
}
>> {foo: "bar", spam: "eggs"}
```

```
# Object with values of mixed types
{
    "object": [
        ["foo", {"literal": null}],
        ["bar", {"literal": 1}],
        ["baz", {"array": [{"literal": 2}]}]
    ]
}
>> {foo: null, bar: 1, baz: [2]}
```

```
# Nested objects
{
    "object": [
        [
            "foo",
            {
                "object": [["bar", {"literal": "baz"}]]
            }
        ]
    ]
}
>> {foo: {bar: "baz"}}
```

```
# Object with expression keys and values
{
    "defining": [
        ["key", {"literal": "foo"}],
        ["value", {"literal": 42}]
    ],
    "result": {
        "object": [
            [{"name": "key"}, {"name": "value"}]
        ]
    }
}
>> {foo: 42}
```

```
# Object with spread
{
    "object": [
        ["answer", {"literal": 42}],
        {
            "spread": {
                "object": [
                    ["bar", {"literal": 1}],
                    ["baz", {"literal": 2}]
                ]
            }
        },
        ["question", {"literal": 69}]
    ]
}
>> {answer: 42, bar: 1, baz: 2, question: 69}
```

## Defining and Calling Functions

```
# No parameters, no arguments
{
    "defining": [
        ["foo", {"given": {}, "result": {"literal": 42}}]
    ],
    "result": {"calling": {"name": "foo"}}
}
>> 42
```

```
# One positional parameter, one positional argument
{
    "defining": [
        ["foo", {"given": {"params": ["x"]}, "result": {"name": "x"}}]
    ],
    "result": {"calling": {"name": "foo"}, "args": [{"literal": 42}]}
}
>> 42
```

```
# One positional parameter, no arguments
{
    "defining": [
        ["foo", {"given": {"params": ["x"]}, "result": {"name": "x"}}]
    ],
    "result": {"calling": {"name": "foo"}}
}
!! missingArgument {"name": "x"}
```

```
# One optional positional parameter, one positional argument
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "params": [
                        {
                            "name": "x",
                            "defaultValue": {"literal": 73}
                        }
                    ]
                },
                "result": {"name": "x"}
            }
        ]
    ],
    "result": {"calling": {"name": "foo"}, "args": [{"literal": 42}]}
}
>> 42
```

```
# One optional positional parameter, no arguments
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "params": [
                        {
                            "name": "x",
                            "defaultValue": {"literal": 73}
                        }
                    ]
                },
                "result": {"name": "x"}
            }
        ]
    ],
    "result": {"calling": {"name": "foo"}}
}
>> 73
```

```
# Default value referencing a name
{
    "defining": [
        ["foo", {"literal": 73}],
        [
            "bar",
            {
                "given": {
                    "params": [
                        {
                            "name": "x",
                            "defaultValue": {"name": "foo"}
                        }
                    ]
                },
                "result": {"name": "x"}
            }
        ]
    ],
    "result": {"calling": {"name": "bar"}}
}
>> 73
```

```
# Positional rest parameter
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "params": [{"rest": "args"}]
                },
                "result": {
                    "calling": {"name": "length"},
                    "args": [{"name": "args"}]
                }
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "args": [{"literal": 42}, {"literal": 97}]
    }
}
>> 2
```

```
# Spread positional argument
{
    "calling": {"name": "plus"},
    "args": [
        {
            "spread": {
                "array": [
                    {"literal": 1},
                    {"literal": 2}
                ]
            }
        }
    ]
}
>> 3
```

```
# Spread named argument
{
    "calling": {"name": "if"},
    "args": [{"literal": true}],
    "namedArgs": [
        {
            "spread": {
                "object": [
                    ["then", {"given": {}, "result": {"literal": 1}}],
                    ["else", {"given": {}, "result": {"literal": 2}}]
                ]
            }
        }
    ]
}
>> 1
```

```
# Named rest parameter
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "namedParams": [{"rest": "namedArgs"}]
                },
                "result": {
                    "calling": {"name": "at"},
                    "args": [
                        {"name": "namedArgs"},
                        {"literal": "bar"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "namedArgs": [["bar", {"literal": 42}]]
    }
}
>> 42
```

```
# Calling a non-function
{
    "calling": {"literal": 42}
}
!! notCallable {"value": 42}
```

A function body can reference names that were in scope when the function was defined, even if those names are out of scope when the function is called.

```
# Closure
{
    "defining": [
        ["x", {"literal": 73}],
        ["foo", {"given": {"params": ["y"]}, "result": {"name": "x"}}]
    ],
    "result": {"calling": {"name": "foo"}, "args": [{"literal": 42}]}
}
>> 73
```

On the other hand, names that are in scope when the function is called don't leak into the function body.

```
# Leakage
{
    "defining": [
        [
            "leaky",
            {"given": {"params": ["x"]}, "result": {"name": "intruder"}}
        ]
    ],
    "result": {
        "defining": [
            ["intruder", {"literal": 42}]
        ],
        "result": {"calling": {"name": "leaky"}, "args": [{"literal": 73}]}
    }
}
!! nameNotDefined {"name": "intruder"}
```

## Errors

```
# Error short-circuiting through function calls
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

```
# Error short-circuiting through arrays
{
    "array": [
        {
            "calling": {"name": "plus"},
            "args": [{"literal": "foo"}]
        }
    ]
}
!! wrongArgumentType {"value": "foo", "expectedType": "number"}
```

```
# Error short-circuiting through objects
{
    "object": [
        [
            "foo",
            {
                "calling": {"name": "plus"},
                "args": [{"literal": "bar"}]
            }
        ]
    ]
}
!! wrongArgumentType {"value": "bar", "expectedType": "number"}
```

```
# Error catching
{
    "catching": {
        "calling": {"name": "plus"},
        "args": [{"literal": "foo"}]
    }
}
>> error("wrongArgumentType", value: "foo", expectedType: "number")
```
