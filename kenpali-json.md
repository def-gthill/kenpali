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
# Literal false
{"type": "literal", "value": false}
>> false
```

```
# Literal true
{"type": "literal", "value": true}
>> true
```

```
# Literal number
{"type": "literal", "value": 1}
>> 1
```

```
# Literal string
{"type": "literal", "value": "foobar"}
>> "foobar"
```

## Names|names

Names are defined using a _block expression_, which has the form `{"type": "block", "defs": <definitions>, "result": <result>}`. The `<definitions>` node must be an array of pairs, where the first element of each pair is a _name pattern_ and the second element is any expression. The `<result>` node can be any expression. The value of the block expression is the value of the `<result>` node.

The name pattern is usually a string, but other pattern types are possible—see [Arrays](#arrays) and [Objects](#objects) for more complex types of name patterns.

A _name expression_ evaluates to the value previously assigned to the specified name. It has the form `{"type": "name", "name": <name>}`, where the `<name>` is a string.

Examples:

```
# Binding a name
{
    "type": "block",
    "defs": [["foo", {"type": "literal", "value": 42}]],
    "result": {"type": "name", "name": "foo"}
}
>> 42
```

```
# Binding multiple names
{
    "type": "block",
    "defs": [
        ["bar", {"type": "literal", "value": 42}],
        ["baz", {"type": "name", "name": "bar"}],
        ["foo", {"type": "name", "name": "baz"}]
    ],
    "result": {"type": "name", "name": "foo"}
}
>> 42
```

Names are defined in the order they appear in the block. It's an error to reference a name before it is defined.

```
# Name used before assignment
{
    "type": "block",
    "defs": [
        ["foo", {"type": "name", "name": "baz"}],
        ["bar", {"type": "literal", "value": 42}],
        ["baz", {"type": "name", "name": "bar"}]
    ],
    "result": {"type": "name", "name": "foo"}
}
!! nameUsedBeforeAssignment {"name": "baz"}
```

It's an error to declare the same name more than once in the same block.

```
# Name declared more than once in the same scope
{
    "type": "block",
    "defs": [
        ["foo", {"type": "literal", "value": 42}],
        ["foo", {"type": "literal", "value": 97}]
    ],
    "result": {"type": "name", "name": "foo"}
}
!! duplicateName {"name": "foo"}
```

A block creates a _scope_; all names defined within it are only accessible within it.

```
# Scope
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "block",
                "defs": [
                    ["bar", {"type": "literal", "value": 42}]
                ],
                "result": {"type": "literal", "value": null}
            }
        ]
    ],
    "result": {"type": "name", "name": "bar"}
}
!! nameNotDefined {"name": "bar"}
```

But expressions in a block can reference names defined in a containing block.

```
# A name from an enclosing scope
{
    "type": "block",
    "defs": [
        ["foo", {"type": "literal", "value": 42}]
    ],
    "result": {
        "type": "block",
        "defs": [
            ["bar", {"type": "literal", "value": 73}]
        ],
        "result": {"type": "name", "name": "foo"}
    }
}
>> 42
```

A block can define a name that duplicates one in an enclosing block. References to that name evaluate to the _innermost_ accessible version of the name—the inner name _shadows_ the outer one.

```
# Shadowing
{
    "type": "block",
    "defs": [
        ["foo", {"type": "literal", "value": 42}]
    ],
    "result": {
        "type": "block",
        "defs": [
            ["foo", {"type": "literal", "value": 73}]
        ],
        "result": {"type": "name", "name": "foo"}
    }
}
>> 73
```

The name pattern can be `null`, which creates an _expression statement_. An expression statement evaluates the expression (usually for its side effects) and then discards the result.

```
# Expression statements
{
    "type": "block",
    "defs": [
        [null, {"type": "literal", "value": 42}]
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
# Array with elements of mixed types
{
    "type": "array",
    "elements": [
        {"type": "literal", "value": null},
        {"type": "literal", "value": 1},
        {"type": "literal", "value": "foo"}
    ]
}
>> [null, 1, "foo"]
```

```
# Nested arrays
{
    "type": "array",
    "elements": [
        {"type": "array", "elements": [{"type": "literal", "value": 1}]}
    ]
}
>> [[1]]
```

```
# Array containing an expression to evaluate
{
    "type": "block",
    "defs": [["foo", {"type": "literal", "value": 42}]],
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
            {"type": "arrayPattern", "names": ["foo", "bar"]},
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
                    "foo",
                    {
                        "type": "optional",
                        "name": "bar", "defaultValue": {"type": "literal", "value": 73}
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
            {"type": "arrayPattern", "names": ["foo", {"type": "rest", "name": "bar"}, "baz"]},
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

An _object expression_ has the form `{"type": "object", "entries": <entries>}`. The `<entries>` node is a JSON array containing name-value pairs. In each pair, the first element can be a string or an expression evaluating to a string, while the second element can be any expression.

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
        ["foo", {"type": "literal", "value": "bar"}],
        ["spam", {"type": "literal", "value": "eggs"}]
    ]
}
>> {foo: "bar", spam: "eggs"}
```

```
# Object with explicit literal keys
{
    "type": "object",
    "entries": [
        [{"type": "literal", "value": "foo"}, {"type": "literal", "value": "bar"}],
        [{"type": "literal", "value": "spam"}, {"type": "literal", "value": "eggs"}]
    ]
}
>> {foo: "bar", spam: "eggs"}
```

```
# Object with values of mixed types
{
    "type": "object",
    "entries": [
        ["foo", {"type": "literal", "value": null}],
        ["bar", {"type": "literal", "value": 1}],
        ["baz", {"type": "array", "elements": [{"type": "literal", "value": 2}]}]
    ]
}
>> {foo: null, bar: 1, baz: [2]}
```

```
# Nested objects
{
    "type": "object",
    "entries": [
        [
            "foo",
            {
                "type": "object",
                "entries": [["bar", {"type": "literal", "value": "baz"}]]
            }
        ]
    ]
}
>> {foo: {bar: "baz"}}
```

```
# Object with expression keys and values
{
    "type": "block",
    "defs": [
        ["key", {"type": "literal", "value": "foo"}],
        ["value", {"type": "literal", "value": 42}]
    ],
    "result": {
        "type": "object",
        "entries": [
            [{"type": "name", "name": "key"}, {"type": "name", "name": "value"}]
        ]
    }
}
>> {foo: 42}
```

Instead of a name-value pair, any of the object's elements can be a _spread_ instead. A spread has the form `{"type": "spread", "value": <value>}`, where `<value>` is any expression. The `<value>` expression is expected to evaluate to an object, whose entries are added to the containing object.

```
# Object with spread
{
    "type": "object",
    "entries": [
        ["answer", {"type": "literal", "value": 42}],
        {
            "type": "spread",
            "value": {
                "type": "object",
                "entries": [
                    ["bar", {"type": "literal", "value": 1}],
                    ["baz", {"type": "literal", "value": 2}]
                ]
            }
        },
        ["question", {"type": "literal", "value": 69}]
    ]
}
>> {answer: 42, bar: 1, baz: 2, question: 69}
```

When defining [names](#names), an _object pattern_ can be used to extract property values from an object and assign them to names. An object pattern has the form `{"type": "objectPattern", "names": <names>}`, where `<names>` is an array of property names to extract.

```
# Object destructuring
{
    "type": "block",
    "defs": [
        [
            {"type": "objectPattern", "names": ["foo", "bar"]},
            {
                "type": "object",
                "entries": [
                    ["bar", {"type": "literal", "value": 42}],
                    ["foo", {"type": "literal", "value": 97}]
                ]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "foo"},
            {"type": "name", "name": "foo"},
            {"type": "name", "name": "bar"}
        ]
    }
}
>> [97, 97, 42]
```

One of the elements of an object pattern can be a _rest pattern_, of the form `{"type": "rest", "name": <name>}`. The `<name>` is bound to an object containing whatever properties aren't mentioned explicitly in the object pattern.

```
# Object destructuring with rest
{
    "type": "block",
    "defs": [
        [
            {"type": "objectPattern", "names": ["bar", {"type": "rest", "name": "others"}]},
            {
                "type": "object",
                "entries": [
                    ["baz", {"type": "literal", "value": 216}],
                    ["bar", {"type": "literal", "value": 42}],
                    ["foo", {"type": "literal", "value": 97}]
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

Any of an object pattern's elements can be an _alias pattern_, of the form `{"name": <pattern>, "property": <property-name>}`. The property `<property-name>` is extracted and bound to `<pattern>`, which can be any name pattern.

```
# Object destructuring with aliases
{
    "type": "block",
    "defs": [
        [
            {
                "type": "objectPattern",
                "names": [
                    {"name": "spam", "property": "foo"},
                    {"name": "eggs", "property": "bar"}
                ]
            },
            {
                "type": "object",
                "entries": [
                    ["foo", {"type": "literal", "value": 42}],
                    ["bar", {"type": "literal", "value": 97}]
                ]
            }
        ]
    ],
    "result": {
        "type": "array",
        "elements": [
            {"type": "name", "name": "eggs"},
            {"type": "name", "name": "eggs"},
            {"type": "name", "name": "spam"}
        ]
    }
}
>> [97, 97, 42]
```

## Defining and Calling Functions|functions

Functions are defined using a _function expression_, which has the form `{"type": "function", "params": <pos-param-spec>, "namedParams": <named-param-spec>, "body": <body>}`. Both `<pos-param-spec>` and `<named-param-spec>` are optional. The `<pos-param-spec>` indicates the positional parameters the function accepts, with the same format as the value in an [array pattern](#arrays); the `<named-param-spec>` indicates the named parameters the function accepts, with the same format as the value in an [object pattern](#objects). The `<body>` can be any expression, and it defines what the function returns. It can reference the parameters as if they were names defined in the function's scope.

Functions are called using a _call expression_, which has the form `{"type": "call", "callee": <callee>, "args": <pos-arg-spec>, "namedArgs": <named-arg-spec>}`. Both `args` and `namedArgs` are optional. The `<callee>` must be an expression, and its result is the function to call. The `<pos-arg-spec>` indicates the positional arguments to pass to the function, with the same format as the value in an [array expression](#arrays); the `<named-arg-spec>` indicates the named arguments to pass to the function, with the same format as the value in an [object expression](#objects).

```
# No parameters, no arguments
{
    "type": "block",
    "defs": [
        ["foo", {"type": "function", "body": {"type": "literal", "value": 42}}]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}}
}
>> 42
```

```
# One positional parameter, one positional argument
{
    "type": "block",
    "defs": [
        ["foo", {"type": "function", "params": ["x"], "body": {"type": "name", "name": "x"}}]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "args": [{"type": "literal", "value": 42}]
    }
}
>> 42
```

```
# One positional parameter, no arguments
{
    "type": "block",
    "defs": [
        ["foo", {"type": "function", "params": ["x"], "body": {"type": "name", "name": "x"}}]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}}
}
!! missingArgument {"name": "x"}
```

```
# One optional positional parameter, one positional argument
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "params": [
                    {
                        "type": "optional",
                        "name": "x",
                        "defaultValue": {"type": "literal", "value": 73}
                    }
                ],
                "body": {"type": "name", "name": "x"}
            }
        ]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}, "args": [{"type": "literal", "value": 42}]}
}
>> 42
```

```
# One optional positional parameter, no arguments
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "params": [
                    {
                        "type": "optional",
                        "name": "x",
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
# Default value referencing a name
{
    "type": "block",
    "defs": [
        ["foo", {"type": "literal", "value": 73}],
        [
            "bar",
            {
                "type": "function",
                "params": [
                    {
                        "type": "optional",
                        "name": "x",
                        "defaultValue": {"type": "name", "name": "foo"}
                    }
                ],
                "body": {"type": "name", "name": "x"}
            }
        ]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "bar"}}
}
>> 73
```

```
# Positional rest parameter
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "params": [{"type": "rest", "name": "args"}],
                "body": {"type": "name", "name": "args"}
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "args": [{"type": "literal", "value": 42}, {"type": "literal", "value": 97}]
    }
}
>> [42, 97]
```

```
# Spread positional argument
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "params": ["bar", "baz"],
                "body": {
                    "type": "array",
                    "elements": [
                        {"type": "name", "name": "baz"},
                        {"type": "name", "name": "baz"},
                        {"type": "name", "name": "bar"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "args": [
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
>> [97, 97, 42]
```

```
# One named parameter, one argument with that name
{
    "type": "block",
    "defs": [
        ["foo", {"type": "function", "namedParams": ["x"], "body": {"type": "name", "name": "x"}}]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "namedArgs": [["x", {"type": "literal", "value": 42}]]
    }
}
>> 42
```

```
# One named parameter, no arguments
{
    "type": "block",
    "defs": [
        ["foo", {"type": "function", "namedParams": ["x"], "body": {"type": "name", "name": "x"}}]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}}
}
!! missingArgument {"name": "x"}
```

```
# One named parameter, one positional argument
{
    "type": "block",
    "defs": [
        ["foo", {"type": "function", "namedParams": ["x"], "body": {"type": "name", "name": "x"}}]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "args": [{"type": "literal", "value": 42}]
    }
}
!! missingArgument {"name": "x"}
```

```
# Named rest parameter
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "namedParams": [{"type": "rest", "name": "namedArgs"}],
                "body": {"type": "name", "name": "namedArgs"}
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "namedArgs": [
            ["bar", {"type": "literal", "value": 42}],
            ["baz", {"type": "literal", "value": 97}]
        ]
    }
}
>> {bar: 42, baz: 97}
```

```
# Spread named argument
{
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "namedParams": ["bar", "baz"],
                "body": {
                    "type": "array",
                    "elements": [
                        {"type": "name", "name": "baz"},
                        {"type": "name", "name": "baz"},
                        {"type": "name", "name": "bar"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "namedArgs": [
            {
                "type": "spread",
                "value": {
                    "type": "object",
                    "entries": [
                        ["bar", {"type": "literal", "value": 42}],
                        ["baz", {"type": "literal", "value": 97}]
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
    "type": "block",
    "defs": [
        [
            "foo",
            {
                "type": "function",
                "params": [
                    {"type": "arrayPattern", "names": ["foo", "bar"]}
                ],
                "body": {
                    "type": "array",
                    "elements": [
                        {"type": "name", "name": "bar"},
                        {"type": "name", "name": "bar"},
                        {"type": "name", "name": "foo"}
                    ]
                }
            }
        ]
    ],
    "result": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "args": [
            {
                "type": "array",
                "elements": [
                    {"type": "literal", "value": 42},
                    {"type": "literal", "value": 97}
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
    "type": "call",
    "callee": {"type": "literal", "value": 42}
}
!! notCallable {"value": 42}
```

A function can reference names defined in an enclosing scope.

```
# A name from an enclosing function
{
    "type": "block",
    "defs": [
        ["x", {"type": "literal", "value": 73}],
        [
            "foo",
            {
                "type": "function",
                "params": ["y"],
                "body": {
                    "type": "array",
                    "elements": [
                        {"type": "name", "name": "x"},
                        {"type": "name", "name": "y"}
                    ]
                }
            }
        ]
    ],
    "result": {"type": "call", "callee": {"type": "name", "name": "foo"}, "args": [{"type": "literal", "value": 42}]}

}
>> [73, 42]
```

A function can reference names that were in scope when the function was _defined_, even if those names are out of scope when the function is _called_.

```
# Closure
{
    "type": "call",
    "callee": {
        "type": "call",
        "callee": {
            "type": "function",
            "body": {
                "type": "block",
                "defs": [["x", {"type": "literal", "value": 73}]],
                "result": {
                    "type": "function",
                    "params": ["y"],
                    "body": {
                        "type": "array",
                        "elements": [
                            {"type": "name", "name": "x"},
                            {"type": "name", "name": "y"}
                        ]
                    }
                }
            }
        }
    },
    "args": [{"type": "literal", "value": 42}]
}
>> [73, 42]
```

On the other hand, names that are in scope when the function is called don't leak into the function body.

```
# Leakage
{
    "type": "block",
    "defs": [
        [
            "leaky",
            {"type": "function", "params": ["x"], "body": {"type": "name", "name": "intruder"}}
        ]
    ],
    "result": {
        "type": "block",
        "defs": [
            ["intruder", {"type": "literal", "value": 42}]
        ],
        "result": {"type": "call", "callee": {"type": "name", "name": "leaky"}, "args": [{"type": "literal", "value": 73}]}
    }
}
!! nameNotDefined {"name": "intruder"}
```

## Indexing|indexing

An _index expression_ extracts the value at a specific index from a collection. It has the form `{"type": "index", "collection": <collection>, "index": <index>}`, where both `<collection>` and `<index>` are expressions.

All numeric indexes are one-based.

```
# Indexing strings
{
    "type": "index",
    "collection": {"type": "literal", "value": "foobar"},
    "index": {"type": "literal", "value": 4}
}
>> "b"
```

```
# Indexing strings - wrong index type
{
    "type": "index",
    "collection": {"type": "literal", "value": "foobar"},
    "index": {"type": "literal", "value": "baz"}
}
!! wrongType {"value": "baz", "expectedType": "number"}
```

```
# Indexing arrays
{
    "type": "index",
    "collection": {
        "type": "array",
        "elements": [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ]
    },
    "index": {"type": "literal", "value": 2}
}
>> "bar"
```

```
# Indexing arrays - index from end
{
    "type": "index",
    "collection": {
        "type": "array",
        "elements": [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ]
    },
    "index": {"type": "literal", "value": -2}
}
>> "foo"
```

```
# Indexing arrays - wrong index type
{
    "type": "index",
    "collection": {
        "type": "array",
        "elements": [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ]
    },
    "index": {"type": "literal", "value": "baz"}
}
!! wrongType {"value": "baz", "expectedType": "number"}
```

```
# Indexing arrays - index less than minus length
{
    "type": "index",
    "collection": {
        "type": "array",
        "elements": [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ]
    },
    "index": {"type": "literal", "value": -3}
}
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": -3}
```

```
# Indexing arrays - index 0
{
    "type": "index",
    "collection": {
        "type": "array",
        "elements": [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ]
    },
    "index": {"type": "literal", "value": 0}
}
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 0}
```

```
# Indexing arrays - index greater than length
{
    "type": "index",
    "collection": {
        "type": "array",
        "elements": [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": "bar"}
        ]
    },
    "index": {"type": "literal", "value": 3}
}
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 3}
```

```
# Indexing objects
{
    "type": "index",
    "collection": {
        "type": "object",
        "entries": [
            ["foo", {"type": "literal", "value": "bar"}],
            ["spam", {"type": "literal", "value": "eggs"}]
        ]
    },
    "index": {"type": "literal", "value": "spam"}
}
>> "eggs"
```

```
# Indexing objects - wrong index type
{
    "type": "index",
    "collection": {
        "type": "object",
        "entries": [
            ["foo", {"type": "literal", "value": "bar"}],
            ["spam", {"type": "literal", "value": "eggs"}]
        ]
    },
    "index": {"type": "literal", "value": 42}
}
!! wrongType {"value": 42, "expectedType": "string"}
```

```
# Indexing objects - property not in object
{
    "type": "index",
    "collection": {
        "type": "object",
        "entries": [
            ["foo", {"type": "literal", "value": "bar"}],
            ["spam", {"type": "literal", "value": "eggs"}]
        ]
    },
    "index": {"type": "literal", "value": "baz"}
}
!! missingProperty {"value": {"foo": "bar", "spam": "eggs"}, "key": "baz"}
```

```
# Indexing something non-indexable
{
    "type": "index",
    "collection": {"type": "literal", "value": 42},
    "index": {"type": "literal", "value": 2}
}
!! wrongType {"value": 42, "expectedType": {"either": ["sequence", "object"]}}
```

## Errors|errors

If an expression throws an error, that error propagates outward through enclosing expressions, aborting further evaluation.

```
# Error short-circuiting through function calls
{
    "type": "call",
    "callee": {
        "type": "function",
        "body": {"type": "literal", "value": 42}
    },
    "args": [
        {
            "type": "block",
            "defs": [
                [{"type": "arrayPattern", "names": ["foo"]}, {"type": "array", "elements": []}]
            ],
            "result": {"type": "name", "name": "foo"}
        }
    ]
}
!! missingElement {"value": [], "name": "foo"}
```

```
# Error short-circuiting through arrays
{
    "type": "array",
    "elements": [
        {
            "type": "block",
            "defs": [
                [{"type": "arrayPattern", "names": ["foo"]}, {"type": "array", "elements": []}]
            ],
            "result": {"type": "name", "name": "foo"}
        }
    ]
}
!! missingElement {"value": [], "name": "foo"}
```

```
# Error short-circuiting through objects
{
    "type": "object",
    "entries": [
        [
            "foo",
            {
                "type": "block",
                "defs": [
                    [{"type": "arrayPattern", "names": ["foo"]}, {"type": "array", "elements": []}]
                ],
                "result": {"type": "name", "name": "foo"}
            }
        ]
    ]
}
!! missingElement {"value": [], "name": "foo"}
```

But if the error encounters a _catch expression_, the catch expression returns the error as a value, stopping its propagation. A catch expression has the form `{"type": "catch", "expression": <expression>}`.

```
# Error catching
{
    "type": "call",
    "callee": {
        "type": "function",
        "body": {"type": "literal", "value": 42}
    },
    "args": [
        {
            "type": "catch",
            "expression": {
                "type": "block",
                "defs": [
                    [{"type": "arrayPattern", "names": ["foo"]}, {"type": "array", "elements": []}]
                ],
                "result": {"type": "name", "name": "foo"}
            }
        }
    ]
}
>> 42
```
