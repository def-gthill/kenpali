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
        "array": [
            {"name": "bar"},
            {"name": "bar"},
            {"name": "foo"}
        ]
    }
}
>> [97, 97, 42]
```

```
# Array destructuring with rest
{
    "defining": [
        [
            {"arrayPattern": ["foo", {"rest": "bar"}, "baz"]},
            {
                "array": [
                    {"literal": 42},
                    {"literal": 1},
                    {"literal": 2},
                    {"literal": 3},
                    {"literal": 97}
                ]
            }
        ]
    ],
    "result": {
        "array": [
            {"name": "baz"},
            {"name": "foo"},
            {"name": "bar"}
        ]
    }
}
>> [97, 42, [1, 2, 3]]
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

```
# Object destructuring
{
    "defining": [
        [
            {"objectPattern": ["foo", "bar"]},
            {
                "object": [
                    ["bar", {"literal": 42}],
                    ["foo", {"literal": 97}]
                ]
            }
        ]
    ],
    "result": {
        "array": [
            {"name": "foo"},
            {"name": "foo"},
            {"name": "bar"}
        ]
    }
}
>> [97, 97, 42]
```

```
# Object destructuring with rest
{
    "defining": [
        [
            {"objectPattern": ["bar", {"rest": "others"}]},
            {
                "object": [
                    ["baz", {"literal": 216}],
                    ["bar", {"literal": 42}],
                    ["foo", {"literal": 97}]
                ]
            }
        ]
    ],
    "result": {
        "array": [
            {"name": "bar"},
            {"name": "others"}
        ]
    }
}
>> [42, {baz: 216, foo: 97}]
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
    "result": {
        "calling": {"name": "foo"},
        "args": [{"literal": 42}]
    }
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
                "result": {"name": "args"}
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "args": [{"literal": 42}, {"literal": 97}]
    }
}
>> [42, 97]
```

```
# Spread positional argument
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "params": ["bar", "baz"]
                },
                "result": {
                    "array": [
                        {"name": "baz"},
                        {"name": "baz"},
                        {"name": "bar"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "args": [
            {
                "spread": {
                    "array": [
                        {"literal": 42},
                        {"literal": 97}
                    ]
                }
            }
        ]
    }
}
>> [97, 97, 42]
```

```
# One named parameter, one argument with that name
{
    "defining": [
        ["foo", {"given": {"namedParams": ["x"]}, "result": {"name": "x"}}]
    ],
    "result": {
        "calling": {"name": "foo"},
        "namedArgs": [["x", {"literal": 42}]]
    }
}
>> 42
```

```
# One named parameter, no arguments
{
    "defining": [
        ["foo", {"given": {"namedParams": ["x"]}, "result": {"name": "x"}}]
    ],
    "result": {"calling": {"name": "foo"}}
}
!! missingArgument {"name": "x"}
```

```
# One named parameter, one positional argument
{
    "defining": [
        ["foo", {"given": {"namedParams": ["x"]}, "result": {"name": "x"}}]
    ],
    "result": {
        "calling": {"name": "foo"},
        "args": [{"literal": 42}]
    }
}
!! missingArgument {"name": "x"}
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
                "result": {"name": "namedArgs"}
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "namedArgs": [
            ["bar", {"literal": 42}],
            ["baz", {"literal": 97}]
        ]
    }
}
>> {bar: 42, baz: 97}
```

```
# Spread named argument
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "namedParams": ["bar", "baz"]
                },
                "result": {
                    "array": [
                        {"name": "baz"},
                        {"name": "baz"},
                        {"name": "bar"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "namedArgs": [
            {
                "spread": {
                    "object": [
                        ["bar", {"literal": 42}],
                        ["baz", {"literal": 97}]
                    ]
                }
            }
        ]
    }
}
>> [97, 97, 42]
```

```
# Calling a non-function
{
    "calling": {"literal": 42}
}
!! notCallable {"value": 42}
```

```
# A name from an enclosing function
{
    "defining": [
        ["x", {"literal": 73}],
        [
            "foo",
            {
                "given": {"params": ["y"]},
                "result": {
                    "array": [
                        {"name": "x"},
                        {"name": "y"}
                    ]
                }
            }
        ]
    ],
    "result": {"calling": {"name": "foo"}, "args": [{"literal": 42}]}

}
>> [73, 42]
```

A function body can reference names that were in scope when the function was defined, even if those names are out of scope when the function is called.

```
# Closure
{
    "calling": {
        "calling": {
            "given": {},
            "result": {
                "defining": [["x", {"literal": 73}]],
                "result": {
                    "given": {"params": ["y"]},
                    "result": {
                        "array": [
                            {"name": "x"},
                            {"name": "y"}
                        ]
                    }
                }
            }
        }
    },
    "args": [{"literal": 42}]
}
>> [73, 42]
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

## Indexing

```
# Indexing strings
{
    "indexing": {"literal": "foobar"},
    "at": {"literal": 4}
}
>> "b"
```

```
# Indexing strings - wrong index type
{
    "indexing": {"literal": "foobar"},
    "at": {"literal": "baz"}
}
!! wrongType {"value": "baz", "expectedType": "number"}
```

```
# Indexing arrays
{
    "indexing": {
        "array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]
    },
    "at": {"literal": 2}
}
>> "bar"
```

```
# Indexing arrays - index from end
{
    "indexing": {
        "array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]
    },
    "at": {"literal": -2}
}
>> "foo"
```

```
# Indexing arrays - wrong index type
{
    "indexing": {
        "array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]
    },
    "at": {"literal": "baz"}
}
!! wrongType {"value": "baz", "expectedType": "number"}
```

```
# Indexing arrays - index less than minus length
{
    "indexing": {
        "array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]
    },
    "at": {"literal": -3}
}
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": -3}
```

```
# Indexing arrays - index 0
{
    "indexing": {
        "array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]
    },
    "at": {"literal": 0}
}
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 0}
```

```
# Indexing arrays - index greater than length
{
    "indexing": {
        "array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]
    },
    "at": {"literal": 3}
}
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 3}
```

```
# Indexing objects
{
    "indexing": {
        "object": [
            ["foo", {"literal": "bar"}],
            ["spam", {"literal": "eggs"}]
        ]
    },
    "at": {"literal": "spam"}
}
>> "eggs"
```

```
# Indexing objects - wrong index type
{
    "indexing": {
        "object": [
            ["foo", {"literal": "bar"}],
            ["spam", {"literal": "eggs"}]
        ]
    },
    "at": {"literal": 42}
}
!! wrongType {"value": 42, "expectedType": "string"}
```

```
{
    "indexing": {
        "object": [
            ["foo", {"literal": "bar"}],
            ["spam", {"literal": "eggs"}]
        ]
    },
    "at": {"literal": "baz"}
}
!! missingProperty {"value": {foo: "bar", spam: "eggs"}, "key": "baz"}
```

```
# Indexing something non-indexable
{
    "indexing": {"literal": 42},
    "at": {"literal": 2}
}
!! wrongType {"value": 42, "expectedType": {"either": ["string", "array", "object"]}}
```

## Errors

```
# Error short-circuiting through function calls
{
    "calling": {
        "given": {},
        "result": {"literal": 42}
    },
    "args": [
        {
            "defining": [
                [{"arrayPattern": ["foo"]}, {"array": []}]
            ],
            "result": {"name": "foo"}
        }
    ]
}
!! missingElement {"name": "foo"}
```

```
# Error short-circuiting through arrays
{
    "array": [
        {
            "defining": [
                [{"arrayPattern": ["foo"]}, {"array": []}]
            ],
            "result": {"name": "foo"}
        }
    ]
}
!! missingElement {"name": "foo"}
```

```
# Error short-circuiting through objects
{
    "object": [
        [
            "foo",
            {
                "defining": [
                    [{"arrayPattern": ["foo"]}, {"array": []}]
                ],
                "result": {"name": "foo"}
            }
        ]
    ]
}
!! missingElement {"name": "foo"}
```

```
# Error catching
{
    "calling": {
        "given": {},
        "result": {"literal": 42}
    },
    "args": [
        {
            "catching": {
                "defining": [
                    [{"arrayPattern": ["foo"]}, {"array": []}]
                ],
                "result": {"name": "foo"}
            }
        }
    ]
}
>> 42
```
