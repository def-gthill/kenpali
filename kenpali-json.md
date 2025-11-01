# Kenpali JSON Specification

## Literals|literals

A _literal expression_ has the form `{"type": "literal", "value": <value>}`. The value can be `null`, `true`, `false`, or [any valid JSON string or number](https://www.json.org/json-en.html).

Examples:

```
# Literal null
{"type": "literal", "value": null}
>> null
```

```
# Literal true
{"type": "literal", "value": true}
>> true
```

```
# Literal string
{"type": "literal", "value": "foobar"}
>> "foobar"
```

## Names|names

Names are defined using a _block expression_, which has the form `{"type": "block", "defs": <definitions>, "result": <result>}`. The `<definitions>` node must be an array of pairs, where the first element of each pair is a _name pattern_ and the second element is any expression. The `<result>` node can be any expression. The value of the block expression is the value of the `<result>` node.

The simplest name pattern has the form `{"type": "name", "name": <name>}`, where the `<name>` is a string. This defines a single name, which is bound to the value of the expression in the second element of the pair. See [Arrays](#arrays) and [Objects](#objects) for more complex types of name patterns.

A _name expression_ evaluates to the value previously assigned to the specified name. It also has the form `{"type": "name", "name": <name>}`, where the `<name>` is a string.

Examples:

```
# Binding a name
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {"type": "literal", "value": 42}
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
>> 42
```

```
# Binding multiple names
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "bar"},
            {"type": "literal", "value": 42}
        ],
        [
            {"type": "name", "name": "baz"},
            {"type": "name", "name": "bar"}
        ],
        [
            {"type": "name", "name": "foo"},
            {"type": "name", "name": "baz"}
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
>> 42
```

Another kind of name pattern has the form `{"type": "ignore"}`. This creates an _expression statement_, which evaluates the expression (usually for its side effects) and then discards the result.

```
# Expression statements
{
    "type": "block",
    "defs": [
        [{"type": "ignore"}, {"type": "literal", "value": 42}]
    ],
    "result": {"type": "literal", "value": 73}
}
>> 73
```

## Arrays|arrays

An _array expression_ has the form `{"type": "array", "elements": <elements>}`. The `<elements>` node is a JSON array containing expressions, which are evaluated to obtain the array's elements.

```
# Empty array
{"type": "array", "elements": []}
>> []
```

```
# Array of literals
{
    "type": "array",
    "elements": [
        {"type": "literal", "value": 1},
        {"type": "literal", "value": 2},
        {"type": "literal", "value": 3}
    ]
}
>> [1, 2, 3]
```

```
# Array containing an expression to evaluate
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {"type": "literal", "value": 42}
        ]
    ],
    "result": {
        "type": "array",
        "elements": [{"type": "name", "name": "foo"}]
    }
}
>> [42]
```

Instead of an expression, any of the array's elements can be a _spread_ instead. A spread has the form `{"type": "spread", "value": <value>}`, where `<value>` is any expression. The `<value>` expression is expected to evaluate to a sequence, whose elements are added to the array.

```
# Array with spread
{
    "type": "array",
    "elements": [
        {"type": "literal", "value": 42},
        {
            "type": "spread",
            "value": {
                "type": "array",
                "elements": [
                    {"type": "literal", "value": 1},
                    {"type": "literal", "value": 2},
                    {"type": "literal", "value": 3}
                ]
            }
        },
        {"type": "literal", "value": 97}
    ]
}
>> [42, 1, 2, 3, 97]
```

When defining [names](#names), an _array pattern_ can be used to extract individual elements from an array and assign them to names. An array pattern has the form `{"type": "arrayPattern", "names": <names>}`, where `<names>` is an array of name patterns.

```
# Array destructuring
{
    "type": "block",
    "defs": [
        [
            {
                "type": "arrayPattern", 
                "names": [
                    {"type": "name", "name": "foo"},
                    {"type": "name", "name": "bar"}
                ]
            },
            {
                "type": "array",
                "elements": [
                    {"type": "literal", "value": 42},
                    {"type": "literal", "value": 97}
                ]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "bar"},
            {"type": "name", "name": "bar"},
            {"type": "name", "name": "foo"}
        ]
    }
}
>> [97, 97, 42]
```

Wrapping one of the names in an _optional pattern_ allows the assignment to work even if there aren't enough elements in the array. An optional pattern has the form `{"type": "optional", "name": <name>, "defaultValue": <defaultValue>}`, where `<name>` is a name pattern and `<defaultValue>` is an expression to evaludate if the array doesn't have enough elements to provide a value for the name.

```
# Array destructuring with optional
{
    "type": "block",
    "defs": [
        [
            {
                "type": "arrayPattern",
                "names": [
                    {"type": "name", "name": "foo"},
                    {
                        "type": "optional",
                        "name": {"type": "name", "name": "bar"},
                        "defaultValue": {"type": "literal", "value": 73}
                    }
                ]
            },
            {
                "type": "array",
                "elements": [{"type": "literal", "value": 42}]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "bar"},
            {"type": "name", "name": "bar"},
            {"type": "name", "name": "foo"}
        ]
    }
}
>> [73, 73, 42]
```

One of the elements of an array pattern can be a _rest pattern_, of the form `{"type": "rest", "name": <name>}`. The `<name>` is bound to an array containing whatever elements are left over after the other name patterns have taken theirs.

```
# Array destructuring with rest
{
    "type": "block",
    "defs": [
        [
            {
                "type": "arrayPattern",
                "names": [
                    {"type": "name", "name": "foo"},
                    {"type": "rest", "name": {"type": "name", "name": "bar"}},
                    {"type": "name", "name": "baz"}
                ]
            },
            {
                "type": "array",
                "elements": [
                    {"type": "literal", "value": 42},
                    {"type": "literal", "value": 1},
                    {"type": "literal", "value": 2},
                    {"type": "literal", "value": 3},
                    {"type": "literal", "value": 97}
                ]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "baz"},
            {"type": "name", "name": "foo"},
            {"type": "name", "name": "bar"}
        ]
    }
}
>> [97, 42, [1, 2, 3]]
```

## Objects|objects

An _object expression_ has the form `{"type": "object", "entries": <entries>}`. The `<entries>` node is a JSON array containing name-value pairs. In each pair, the first element must be an expression evaluating to a string, while the second element can be any expression.

```
# Empty object
{"type": "object", "entries": []}
>> {}
```

```
# Object with literal values
{
    "type": "object",
    "entries": [
        [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ],
        [
            {"type": "literal", "value": "spam"},
            {"type": "literal", "value": "eggs"}
        ]
    ]
}
>> {foo: "bar", spam: "eggs"}
```

```
# Object with expression keys and values
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "key"},
            {"type": "literal", "value": "foo"}
        ],
        [
            {"type": "name", "name": "value"},
            {"type": "literal", "value": 42}
        ]
    ],
    "result": {
        "type": "object",
        "entries": [
            [
                {"type": "name", "name": "key"},
                {"type": "name", "name": "value"}
            ]
        ]
    }
}
>> {foo: 42}
```

Instead of an expression, a key can be `{"type": "spread"}` instead. The corresponding `<value>` expression is expected to evaluate to an object, whose entries are added to the containing object.

```
# Object with spread
{
    "type": "object",
    "entries": [
        [
            {"type": "literal", "value": "answer"},
            {"type": "literal", "value": 42}
        ],
        [
            {"type": "spread"},
            {
                "type": "object",
                "entries": [
                    [
                        {"type": "literal", "value": "bar"},
                        {"type": "literal", "value": 1}
                    ],
                    [
                        {"type": "literal", "value": "baz"},
                        {"type": "literal", "value": 2}
                    ]
                ]
            }
        ],
        [
            {"type": "literal", "value": "question"},
            {"type": "literal", "value": 69}
        ]
    ]
}
>> {answer: 42, bar: 1, baz: 2, question: 69}
```

When defining [names](#names), an _object pattern_ can be used to extract property values from an object and assign them to names. An object pattern has the form `{"type": "objectPattern", "entries": <entries>}`. Each entry is a pair whose first element is an expression yielding the property name to extract, and whose second element is a name pattern to bind the property value to.

```
# Object destructuring
{
    "type": "block",
    "defs": [
        [
            {
                "type": "objectPattern",
                "entries": [
                    [
                        {"type": "literal", "value": "foo"}, {"type": "name", "name": "foo"}
                    ],
                    [
                        {"type": "literal", "value": "bar"}, {"type": "name", "name": "qux"}
                    ]
                ]
            },
            {
                "type": "object",
                "entries": [
                    [
                        {"type": "literal", "value": "bar"},
                        {"type": "literal", "value": 42}
                    ],
                    [
                        {"type": "literal", "value": "foo"},
                        {"type": "literal", "value": 97}
                    ]
                ]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "foo"},
            {"type": "name", "name": "foo"},
            {"type": "name", "name": "qux"}
        ]
    }
}
>> [97, 97, 42]
```

One of the entries of an object pattern can have `{"type": "rest"}` as its key. The corresponding name pattern is bound to an object containing whatever properties aren't mentioned explicitly in the object pattern.

```
# Object destructuring with rest
{
    "type": "block",
    "defs": [
        [
            {
                "type": "objectPattern",
                "entries": [
                    [
                        {"type": "literal", "value": "bar"},
                        {"type": "name", "name": "bar"}
                    ],
                    [
                        {"type": "rest"},
                        {"type": "name", "name": "others"}
                    ]
                ]
            },
            {
                "type": "object",
                "entries": [
                    [
                        {"type": "literal", "value": "baz"},
                        {"type": "literal", "value": 216}
                    ],
                    [
                        {"type": "literal", "value": "bar"},
                        {"type": "literal", "value": 42}
                    ],
                    [
                        {"type": "literal", "value": "foo"},
                        {"type": "literal", "value": 97}
                    ]
                ]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "bar"},
            {"type": "name", "name": "others"}
        ]
    }
}
>> [42, {baz: 216, foo: 97}]
```

## Defining and Calling Functions|functions

Functions are defined using a _function expression_, which has the form `{"type": "function", "posParams": <pos-param-spec>, "namedParams": <named-param-spec>, "body": <body>}`. Both `<pos-param-spec>` and `<named-param-spec>` are optional. The `<pos-param-spec>` indicates the positional parameters the function accepts, with the same format as the `names` in an [array pattern](#arrays); the `<named-param-spec>` indicates the named parameters the function accepts, with the same format as the `entries` in an [object pattern](#objects). The `<body>` can be any expression, and it defines what the function returns. It can reference the parameters as if they were names defined in the function's scope.

Functions are called using a _call expression_, which has the form `{"type": "call", "callee": <callee>, "posArgs": <pos-arg-spec>, "namedArgs": <named-arg-spec>}`. Both `posArgs` and `namedArgs` are optional. The `<callee>` must be an expression, and its result is the function to call. The `<pos-arg-spec>` indicates the positional arguments to pass to the function, with the same format as the value in an [array expression](#arrays); the `<named-arg-spec>` indicates the named arguments to pass to the function, with the same format as the value in an [object expression](#objects).

```
# No parameters, no arguments
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {"type": "function", "body": {"type": "literal", "value": 42}}
        ]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}}
}
>> 42
```

```
# Positional parameter and positional argument
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "function",
                "posParams": [{"type": "name", "name": "x"}],
                "body": {"type": "name", "name": "x"}
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "posArgs": [{"type": "literal", "value": 42}]
    }
}
>> 42
```

```
# Optional positional parameter
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "function",
                "posParams": [
                    {
                        "type": "optional",
                        "name": {"type": "name", "name": "x"},
                        "defaultValue": {"type": "literal", "value": 73}
                    }
                ],
                "body": {"type": "name", "name": "x"}
            }
        ]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}}
}
>> 73
```

```
# Positional rest parameter and spread argument
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "function",
                "posParams": [{"type": "rest", "name": {"type": "name", "name": "args"}}],
                "body": {"type": "name", "name": "args"}
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "posArgs": [
            {
                "type": "spread",
                "value": {
                    "type": "array",
                    "elements": [
                        {"type": "literal", "value": 42},
                        {"type": "literal", "value": 97}
                    ]
                }
            }
        ]
    }
}
>> [42, 97]
```

```
# Named parameter and named argument
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "function",
                "namedParams": [
                    [
                        {"type": "literal", "value": "x"},
                        {"type": "name", "name": "x"}
                    ]
                ],
                "body": {"type": "name", "name": "x"}
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "namedArgs": [
            [
                {"type": "literal", "value": "x"},
                {"type": "literal", "value": 42}
            ]
        ]
    }
}
>> 42
```

```
# Expression named argument
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "function",
                "namedParams": [
                    [
                        {"type": "literal", "value": "x"},
                        {"type": "name", "name": "x"}
                    ]
                ],
                "body": {"type": "name", "name": "x"}
            }
        ],
        [
            {"type": "name", "name": "key"},
            {"type": "literal", "value": "x"}
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "namedArgs": [
            [
                {"type": "name", "name": "key"},
                {"type": "literal", "value": 42}
            ]
        ]
    }
}
>> 42
```

```
# Named rest parameter and spread argument
{
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "function",
                "namedParams": [
                    [
                        {"type": "rest"},
                        {"type": "name", "name": "namedArgs"}
                    ]
                ],
                "body": {"type": "name", "name": "namedArgs"}
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "namedArgs": [
            [
                {"type": "spread"},
                {
                    "type": "object",
                    "entries": [
                        [
                            {"type": "literal", "value": "bar"},
                            {"type": "literal", "value": 42}
                        ],
                        [
                            {"type": "literal", "value": "baz"},
                            {"type": "literal", "value": 97}
                        ]
                    ]
                }
            ]
        ]
    }
}
>> {bar: 42, baz: 97}
```

## Indexing|indexing

An _index expression_ extracts the value at a specific index from a collection. It has the form `{"type": "index", "collection": <collection>, "index": <index>}`, where both `<collection>` and `<index>` are expressions.

All numeric indexes are one-based.

```
# Indexing
{
    "type": "index",
    "collection": {"type": "literal", "value": "foobar"},
    "index": {"type": "literal", "value": 4}
}
>> "b"
```

## Errors|errors

If an expression throws an error, that error propagates outward through enclosing expressions, aborting further evaluation. But if the error encounters a _catch expression_, the catch expression returns the error as a value, stopping its propagation. A catch expression has the form `{"type": "catch", "expression": <expression>}`.

```
# Error catching
{
    "type": "call",
    "callee": {
        "type": "function",
        "body": {"type": "literal", "value": 42}
    },
    "posArgs": [
        {
            "type": "catch",
            "expression": {
                "type": "block",
                "defs": [
                    [
                        {
                            "type": "arrayPattern",
                            "names": [{"type": "name", "name": "foo"}]
                        },
                        {"type": "array", "elements": []}
                    ]
                ],
                "result": {"type": "name", "name": "foo"}
            }
        }
    ]
}
>> 42
```
