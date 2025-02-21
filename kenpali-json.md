# Kenpali JSON Specification

## Literals|literals

A _literal expression_ has the form `{"literal": <value>}`. The value can be `null`, `true`, `false`, or [any valid JSON string or number](https://www.json.org/json-en.html).

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

## Names|names

Names are defined using a _defining expression_, which has the form `{"defining": <definitions>, "result": <result>}`. The `<definitions>` node must be an array of pairs, where the first element of each pair is a _name pattern_ and the second element is any expression. The `<result>` node can be any expression. The value of the defining expression is the value of the `<result>` node.

The name pattern is usually a string, but other pattern types are possible—see [Arrays](#arrays) and [Objects](#objects) for more complex types of name patterns.

A _name expression_ evaluates to the value previously assigned to the specified name. It has the form `{"name": <name>}`, where the `<name>` is a string.

Examples:

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

Names are defined in the order they appear in the defining expression. It's an error to reference a name before it is defined.

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

It's an error to declare the same name more than once in the same defining expression.

```
# Name declared more than once in the same scope
{
    "defining": [
        ["foo", {"literal": 42}],
        ["foo", {"literal": 97}]
    ],
    "result": {"name": "foo"}
}
!! duplicateName {"name": "foo"}
```

A defining expression creates a _scope_; all names defined within it are only accessible within it.

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

A defining expression can define a name that duplicates one in an enclosing defining expression. References to that name evaluate to the _innermost_ accessible version of the name—the inner name _shadows_ the outer one.

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

The name pattern can be `null`, which creates an _expression statement_. An expression statement evaluates the expression (usually for its side effects) and then discards the result.

```
# Expression statements
{
    "defining": [
        [null, {"literal": 42}]
    ],
    "result": {"literal": 73}
}
>> 73
```

## Arrays|arrays

An _array expression_ has the form `{"array": <elements>}`. The `<elements>` node is a JSON array containing expressions, which are evaluated to obtain the array's elements.

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

Instead of an expression, any of the array's elements can be a _spread_ instead. A spread has the form `{"spread": <value>}`, where `<value>` is any expression. The `<value>` expression is expected to evaluate to a sequence, whose elements are added to the array.

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

When defining [names](#names), an _array pattern_ can be used to extract individual elements from an array and assign them to names. An array pattern has the form `{"arrayPattern": <names>}`, where `<names>` is an array of name patterns.

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

One of the elements of an array pattern can be a _rest pattern_, of the form `{"rest": <name>}`. The `<name>` is bound to an array containing whatever elements are left over after the other name patterns have taken theirs.

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

## Objects|objects

An _object expression_ has the form `{"object": <entries>}`. The `<entries>` node is a JSON array containing name-value pairs. In each pair, the first element can be a string or an expression evaluating to a string, while the second element can be any expression.

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

Instead of a name-value pair, any of the object's elements can be a _spread_ instead. A spread has the form `{"spread": <value>}`, where `<value>` is any expression. The `<value>` expression is expected to evaluate to an object, whose entries are added to the containing object.

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

When defining [names](#names), an _object pattern_ can be used to extract property values from an object and assign them to names. An object pattern has the form `{"objectPattern": <names>}`, where `<names>` is an array of property names to extract.

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

One of the elements of an object pattern can be a _rest pattern_, of the form `{"rest": <name>}`. The `<name>` is bound to an object containing whatever properties aren't mentioned explicitly in the object pattern.

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

Any of an object pattern's elements can be an _alias pattern_, of the form `{"name": <pattern>, "property": <property-name>}`. The property `<property-name>` is extracted and bound to `<pattern>`, which can be any name pattern.

```
# Object destructuring with aliases
{
    "defining": [
        [
            {
                "objectPattern": [
                    {"name": "spam", "property": "foo"},
                    {"name": "eggs", "property": "bar"}
                ]
            },
            {
                "object": [
                    ["foo", {"literal": 42}],
                    ["bar", {"literal": 97}]
                ]
            }
        ]
    ],
    "result": {
        "array": [
            {"name": "eggs"},
            {"name": "eggs"},
            {"name": "spam"}
        ]
    }
}
>> [97, 97, 42]
```

## Defining and Calling Functions|functions

Functions are defined using a _given expression_, which has the form `{"given": <param-spec>, "result": <body>}`. The `<param-spec>` is an object with two optional properties: `params`, giving the function's positional parameters, with the same format as the value in an [array pattern](#arrays); and `namedParams`, giving the function's named parameters, with the same format as the value in an [object pattern](#objects). The `<body>` can be any expression, and it defines what the function returns.

Functions are called using a _calling expression_, which has the form `{"calling": <function>, "args": <pos-arg-spec>, "namedArgs": <named-arg-spec>}`. Both `args` and `namedArgs` are optional. The `<function>` must be an expression, and its result is the function to call. The `<pos-arg-spec>` indicates the positional arguments to pass to the function, with the same format as the value in an [array expression](#arrays); the `<named-arg-spec>` indicates the named arguments to pass to the function, with the same format as the value in an [object expression](#objects).

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
# Array destructuring in parameters
{
    "defining": [
        [
            "foo",
            {
                "given": {
                    "params": [
                        {"arrayPattern": ["foo", "bar"]}
                    ]
                },
                "result": {
                    "array": [
                        {"name": "bar"},
                        {"name": "bar"},
                        {"name": "foo"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "calling": {"name": "foo"},
        "args": [
            {
                "array": [
                    {"literal": 42},
                    {"literal": 97}
                ]
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

A function can reference names defined in an enclosing scope.

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

A function can reference names that were in scope when the function was _defined_, even if those names are out of scope when the function is _called_.

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

## Indexing|indexing

An _indexing expression_ extracts the value at a specific index from a collection. It has the form `{"indexing": <collection>, "at": <index>}`, where both `<collection>` and `<index>` are expressions.

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
# Indexing objects - property not in object
{
    "indexing": {
        "object": [
            ["foo", {"literal": "bar"}],
            ["spam", {"literal": "eggs"}]
        ]
    },
    "at": {"literal": "baz"}
}
!! missingProperty {"value": {"foo": "bar", "spam": "eggs"}, "key": "baz"}
```

```
# Indexing something non-indexable
{
    "indexing": {"literal": 42},
    "at": {"literal": 2}
}
!! wrongType {"value": 42, "expectedType": {"either": ["sequence", "object"]}}
```

## Errors|errors

If an expression throws an error, that error propagates outward through enclosing expressions, aborting further evaluation.

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
!! missingElement {"value": [], "name": "foo"}
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
!! missingElement {"value": [], "name": "foo"}
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
!! missingElement {"value": [], "name": "foo"}
```

But if the error encounters a _catching expression_, the catching expression returns the error as a value, stopping its propagation. A catching expression has the form `{"catching": <expression>}`.

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
