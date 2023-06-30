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

## Builtins

### Arithmetic

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

### Strings

```
# Length
{
    "calling": {"name": "length"},
    "args": [{"literal": "foo"}]
}
>> 3
```

```
# Join
{
    "calling": {"name": "join"},
    "args": [{"literal": "foo"}, {"literal": "bar"}, {"literal": "baz"}]
}
>> "foobarbaz"
```

### Comparison

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

```
# Less than on numbers
{
    "array": [
        {
            "calling": {"name": "isLessThan"},
            "args": [{"literal": 42}, {"literal": 43}]
        },
        {
            "calling": {"name": "isLessThan"},
            "args": [{"literal": 42}, {"literal": 42}]
        },
        {
            "calling": {"name": "isLessThan"},
            "args": [{"literal": 43}, {"literal": 42}]
        }
    ]
}
>> [true, false, false]
```

### Types and Type Conversion

```
# Type of
{
    "array": [
        {
            "calling": {"name": "typeOf"},
            "args": [{"literal": null}]
        },
        {
            "calling": {"name": "typeOf"},
            "args": [{"literal": false}]
        },
        {
            "calling": {"name": "typeOf"},
            "args": [{"literal": 1}]
        },
        {
            "calling": {"name": "typeOf"},
            "args": [{"literal": "foo"}]
        },
        {
            "calling": {"name": "typeOf"},
            "args": [
                {"array": [{"literal": 1}]}
            ]
        },
        {
            "calling": {"name": "typeOf"},
            "args": [
                {"object": [["foo", {"literal": "bar"}]]}
            ]
        }
    ]
}
>> ["null", "boolean", "number", "string", "array", "object"]
```

```
# To string
{
    "array": [
        {
            "calling": {"name": "toString"},
            "args": [{"literal": null}]
        },
        {
            "calling": {"name": "toString"},
            "args": [{"literal": false}]
        },
        {
            "calling": {"name": "toString"},
            "args": [{"literal": true}]
        },
        {
            "calling": {"name": "toString"},
            "args": [{"literal": 1}]
        },
        {
            "calling": {"name": "toString"},
            "args": [{"literal": -2.5}]
        },
        {
            "calling": {"name": "toString"},
            "args": [{"literal": "foobar"}]
        },
        {
            "calling": {"name": "toString"},
            "args": [{"array": [{"literal": 1}, {"literal": "foobar"}]}]
        },
        {
            "calling": {"name": "toString"},
            "args": [
                {
                    "object": [
                        ["foo", {"literal": "bar"}],
                        ["spam!", {"literal": "eggs"}]
                    ]
                }
            ]
        }
    ]
}
>> [
    "null",
    "false",
    "true",
    "1",
    "-2.5",
    "\"foobar\"",
    "[1, \"foobar\"]",
    "{foo: \"bar\", \"spam!\": \"eggs\"}"
]
```

```
# To number
{
    "array": [
        {
            "calling": {"name": "toNumber"},
            "args": [{"literal": "1"}]
        },
        {
            "calling": {"name": "toNumber"},
            "args": [{"literal": "-2.5"}]
        },
        {
            "calling": {"name": "toNumber"},
            "args": [{"literal": 42}]
        }
    ]
}
>> [1, -2.5, 42]
```

### Control Flow

```
# If
{
    "array": [
        {
            "calling": {"name": "if"},
            "args": [{"literal": true}],
            "namedArgs": {"then": {"literal": 1}, "else": {"literal": 2}}
        },
        {
            "calling": {"name": "if"},
            "args": [{"literal": false}],
            "namedArgs": {"then": {"literal": 1}, "else": {"literal": 2}}
        }
    ]
}
>> [1, 2]
```

```
# Repeat
{
    "calling": {"name": "repeat"},
    "args": [
        {"literal": 1},
        {
            "given": {"params": ["previous"]},
            "result": {
                "object": [
                    [
                        "while",
                        {
                            "calling": {"name": "isLessThan"},
                            "args": [{"name": "previous"}, {"literal": 1000}]
                        }
                    ],
                    [
                        "next",
                        {
                            "calling": {"name": "times"},
                            "args": [{"name": "previous"}, {"literal": 2}]
                        }
                    ]
                ]
            }
        }
    ]
}
>> 1024
```

### Arrays

```
# Indexing arrays
{
    "array": [
        {
            "calling": {"name": "at"},
            "args": [
                {"array": [{"literal": "foo"}, {"literal": "bar"}]},
                {"literal": 2}
            ]
        },
        {
            "calling": {"name": "at"},
            "args": [
                {"array": [{"literal": "foo"}, {"literal": "bar"}]},
                {"literal": 1}
            ]
        }
    ]
}
>> ["bar", "foo"]
```

```
# Length
{
    "calling": {"name": "length"},
    "args": [{"array": [{"literal": "foo"}, {"literal": "bar"}]}]
}
>> 2
```

```
# Build
{
    "calling": {"name": "build"},
    "args": [
        {"array": [{"literal": 1}, {"literal": 1}]},
        {
            "given": {"params": ["previous"]},
            "result": {
                "object": [
                    [
                        "while",
                        {
                            "calling": {"name": "isLessThan"},
                            "args": [
                                {
                                    "calling": {"name": "at"},
                                    "args": [{"name": "previous"}, {"literal": 1}]
                                },
                                {"literal": 20}
                            ]
                        }
                    ],
                    [
                        "out",
                        {
                            "calling": {"name": "at"},
                            "args": [{"name": "previous"}, {"literal": 1}]
                        }
                    ],
                    [
                        "next",
                        {
                            "array": [
                                {
                                    "calling": {"name": "at"},
                                    "args": [{"name": "previous"}, {"literal": 2}]
                                },
                                {
                                    "calling": {"name": "plus"},
                                    "args": [
                                        {
                                            "calling": {"name": "at"},
                                            "args": [{"name": "previous"}, {"literal": 1}]
                                        },
                                        {
                                            "calling": {"name": "at"},
                                            "args": [{"name": "previous"}, {"literal": 2}]
                                        }
                                    ]
                                }
                            ]
                        }
                    ]
                ]
            }
        }
    ]
}
>> [1, 1, 2, 3, 5, 8, 13, 21]
```

### Objects

```
# Indexing objects
{
    "array": [
        {
            "calling": {"name": "at"},
            "args": [
                {
                    "object": [
                        ["foo", {"literal": "bar"}],
                        ["spam", {"literal": "eggs"}]
                    ]
                },
                {"literal": "spam"}
            ]
        },
        {
            "calling": {"name": "at"},
            "args": [
                {
                    "object": [
                        ["foo", {"literal": "bar"}],
                        ["spam", {"literal": "eggs"}]
                    ]
                },
                {"literal": "foo"}
            ]
        }
    ]
}
>> ["eggs", "bar"]
```
