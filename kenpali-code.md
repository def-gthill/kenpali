# Kenpali Code Specification

## Literals

```
# Literal null
null
>> {"literal": null}
```

```
# Literal false
false
>> {"literal": false}
```

```
# Literal true
true
>> {"literal": true}
```

```
# Literal integer
1
>> {"literal": 1}
```

```
# Literal decimal
-2.5
>> {"literal": -2.5}
```

```
# Literal string
"foobar"
>> {"literal": "foobar"}
```

## Names

```
# Name with only letters
foo
>> {"name": "foo"}
```

```
# Name with uppercase letters
FOO
>> {"name": "FOO"}
```

```
# Name with numbers
f00
>> {"name": "f00"}
```

## Arrays

```
# Empty array
[]
>> {"array": []}
```

```
# Single-element array
[1]
>> {"array": [{"literal": 1}]}
```

```
# Array of literals
[1, 2, 3]
>> {"array": [{"literal": 1}, {"literal": 2}, {"literal": 3}]}
```

```
# Trailing comma
[1, 2, 3,]
>> {"array": [{"literal": 1}, {"literal": 2}, {"literal": 3}]}
```

```
# Array with elements of mixed types
[null, 1, "foo"]
>> {"array": [{"literal": null}, {"literal": 1}, {"literal": "foo"}]}
```

```
# Nested arrays
[[1]]
>> {"array": [{"array": [{"literal": 1}]}]}
```

```
# Array containing an expression to evaluate
[foo]
>> {"array": [{"name": "foo"}]}
```

```
# Arrays with spread
foo = [1, 2, 3];
[42, *foo, 97]
>> {
    "defining": {
        "foo": {
            "array": [
                {"literal": 1},
                {"literal": 2},
                {"literal": 3}
            ]
        }
    },
    "result": {
        "array": [
            {"literal": 42},
            {"spread": {"name": "foo"}},
            {"literal": 97}
        ]
    }
}
```

## Objects

```
# Empty object
{}
>> {"object": []}
```

```
# Object with literal values
{"foo": "bar", "spam": "eggs"}
>> {
    "object": [
        ["foo", {"literal": "bar"}],
        ["spam", {"literal": "eggs"}]
    ]
}
```

If a key is a valid Kenpali name, the quotes can be omitted.

```
# Object with shorthand keys
{foo: "bar", spam: "eggs"}
>> {
    "object": [
        ["foo", {"literal": "bar"}],
        ["spam", {"literal": "eggs"}]
    ]
}
```

```
# Object with values of mixed types
{foo: null, bar: 1, baz: [2]}
>> {
    "object": [
        ["foo", {"literal": null}],
        ["bar", {"literal": 1}],
        ["baz", {"array": [{"literal": 2}]}]
    ]
}
```

```
# Nested objects
{foo: {bar: "baz"}}
>> {
    "object": [
        [
            "foo",
            {
                "object": [["bar", {"literal": "baz"}]]
            }
        ]
    ]
}
```

```
# Object with expression keys and values
{<<key>>: value}
>> {"object": [[{"name": "key"}, {"name": "value"}]]}
```

```
# Objects with spread
foo = {bar: 1, baz: 2};
{answer: 42, **foo, question: 69}
>> {
    "defining": {
        "foo": {
            "object": [
                ["bar", {"literal": 1}],
                ["baz", {"literal": 2}]
            ]
        }
    },
    "result": {
        "object": [
            ["answer", {"literal": 42}],
            {"spread": {"name": "foo"}},
            ["question", {"literal": 69}]
        ]
    }
}
```

## Comments

```
# A comment on its own line
// A billion-dollar mistake
null
>> {"literal": null}
```

```
# A comment at the end of a line
null // A billion-dollar mistake
>> {"literal": null}
```

## Function Calls

```
# One positional argument
foo(1)
>> {
    "calling": {"name": "foo"},
    "args": [{"literal": 1}]
}
```

```
# Two positional arguments
foo(1, 2)
>> {
    "calling": {"name": "foo"},
    "args": [{"literal": 1}, {"literal": 2}]
}
```

```
# Spread positional arguments
foo(*bar)
>> {
    "calling": {"name": "foo"},
    "args": [{"spread": {"name": "bar"}}]
}
```

```
# Positional and spread positional arguments
foo(1, *bar)
>> {
    "calling": {"name": "foo"},
    "args": [
        {"literal": 1},
        {"spread": {"name": "bar"}}
    ]
}
```

```
# One named argument
foo(bar: 1)
>> {
    "calling": {"name": "foo"},
    "namedArgs": [["bar", {"literal": 1}]]
}
```

```
# Two named arguments
foo(bar: 1, baz: 2)
>> {
    "calling": {"name": "foo"},
    "namedArgs": [["bar", {"literal": 1}], ["baz", {"literal": 2}]]
}
```

```
# Spread named arguments
foo(**bar)
>> {
    "calling": {"name": "foo"},
    "namedArgs": [{"spread": {"name": "bar"}}]
}
```

```
# Positional and named arguments
foo(1, 2, bar: 3, baz: 4)
>> {
    "calling": {"name": "foo"},
    "args": [{"literal": 1}, {"literal": 2}],
    "namedArgs": [["bar", {"literal": 3}], ["baz", {"literal": 4}]]
}
```

```
# Calling the result of a function call
foo(x)(y)
>> {
    "calling": {
        "calling": {"name": "foo"},
        "args": [{"name": "x"}]
    },
    "args": [{"name": "y"}]
}
```

## Error Catching

```
# Error catching
foo !
>> {
    "catching": {"name": "foo"}
}
```

## Function Definitions

```
# No parameters
() => 42
>> {"given": {}, "result": {"literal": 42}}
```

```
# One positional parameter
(x) => plus(x, 3)
>> {
    "given": {
        "params": ["x"]
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [{"name": "x"}, {"literal": 3}]
    }
}
```

```
# Optional positional parameter
(x, y = 3) => plus(x, y)
>> {
    "given": {
        "params": [
            "x",
            {"name": "y", "defaultValue": {"literal": 3}}
        ]
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [{"name": "x"}, {"name": "y"}]
    }
}
```

```
# Positional rest parameter
(*args) => length(args)
>> {
    "given": {
        "restParam": "args"
    },
    "result": {
        "calling": {"name": "length"},
        "args": [{"name": "args"}]
    }
}
```

```
# Named parameter
(x, y:) => plus(x, y)
>> {
    "given": {
        "params": ["x"],
        "namedParams": ["y"]
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [{"name": "x"}, {"name": "y"}]
    }
}
```

```
# Optional named parameter
(x, y: = 3) => plus(x, y)
>> {
    "given": {
        "params": ["x"],
        "namedParams": [
            {"name": "y", "defaultValue": {"literal": 3}}
        ]
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [{"name": "x"}, {"name": "y"}]
    }
}
```

```
# Named rest parameter
(**namedArgs) => namedArgs
>> {
    "given": {
        "namedRestParam": "namedArgs"
    },
    "result": {"name": "namedArgs"}
}
```

```
# Scope in function body
(x) => (y = plus(x, 3); y)
>> {
    "given": {
        "params": ["x"]
    },
    "result": {
        "defining": {
            "y": {
                "calling": {"name": "plus"},
                "args": [{"name": "x"}, {"literal": 3}]
            }
        },
        "result": {"name": "y"}
    }
}
```

## Forward Pipe

```
# Forward pipe into a bare name
1 | foo
>> {
    "calling": {"name": "foo"},
    "args": [{"literal": 1}]
}
```

```
# Forward pipe injecting a first argument
1 | bar(2)
>> {
    "calling": {"name": "bar"},
    "args": [{"literal": 1}, {"literal": 2}]
}
```

```
# Forward pipe injecting alongside a named argument
1 | bar(foo: 2)
>> {
    "calling": {"name": "bar"},
    "args": [{"literal": 1}],
    "namedArgs": [["foo", {"literal": 2}]]
}
```

```
# Chaining forward pipes
1 | foo | bar(2)
>> {
    "calling": {"name": "bar"},
    "args": [
        {
            "calling": {"name": "foo"},
            "args": [{"literal": 1}]
        },
        {"literal": 2}
    ]
}
```

```
# Blocking first-argument injection
1 | (bar(2))
>> {
    "calling": {
        "calling": {"name": "bar"},
        "args": [{"literal": 2}]
    },
    "args": [{"literal": 1}]
}
```

```
# Pipe in an arrow
(x) => x | plus(3)
>> {
    "given": {
        "params": ["x"]
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [{"name": "x"}, {"literal": 3}]
    }
}
```

```
# Error catching in pipeline
1 | foo ! | bar
>> {
    "calling": {"name": "bar"},
    "args": [
        {
            "catching": {
                "calling": {"name": "foo"},
                "args": [{"literal": 1}]
            }
        }
    ]
}
```

## Indexing

```
# Indexing with @
["foo", "bar"] @ 2
>> {
    "calling": {"name": "at"},
    "args": [
        {"array": [
            {"literal": "foo"},
            {"literal": "bar"}
        ]},
        {"literal": 2}
    ]
}
```

```
# Correct precedence of @
[x @ 1 | f, x | f @ 1]
>> {
    "array": [
        {
            "calling": {"name": "f"},
            "args": [
                {
                    "calling": {"name": "at"},
                    "args": [
                        {"name": "x"},
                        {"literal": 1}
                    ]
                }
            ]
        },
        {
            "calling": {"name": "at"},
            "args": [
                {
                    "calling": {"name": "f"},
                    "args": [{"name": "x"}]
                },
                {"literal": 1}
            ]
        }
    ]
}
```

```
# Indexing with .
foo.bar
>> {
    "calling": {"name": "at"},
    "args": [
        {"name": "foo"},
        {"literal": "bar"}
    ]
}
```

```
# Correct precedence of . and |
[x.y | f, x | y.f]
>> {
    "array": [
        {
            "calling": {"name": "f"},
            "args": [
                {
                    "calling": {"name": "at"},
                    "args": [
                        {"name": "x"},
                        {"literal": "y"}
                    ]
                }
            ]
        },
        {
            "calling": {
                "calling": {"name": "at"},
                "args": [
                    {"name": "y"},
                    {"literal": "f"}
                ]
            },
            "args": [{"name": "x"}]
        }
    ]
}
```

```
# Correct precedence of . and function calls
[x.f(y), f(x).y]
>> {
    "array": [
        {
            "calling": {
                "calling": {"name": "at"},
                "args": [
                    {"name": "x"},
                    {"literal": "f"}
                ]
            },
            "args": [{"name": "y"}]
        },
        {
            "calling": {"name": "at"},
            "args": [
                {
                    "calling": {"name": "f"},
                    "args": [{"name": "x"}]
                },
                {"literal": "y"}
            ]
        }
    ]
}
```

```
# Indexing an expression with .
(x | f).y
>> {
    "calling": {"name": "at"},
    "args": [
        {
            "calling": {"name": "f"},
            "args": [
                {"name": "x"}
            ]
        },
        {"literal": "y"}
    ]
}
```

```
# Chaining indexing with .
x.y.z
>> {
    "calling": {"name": "at"},
    "args": [
        {
            "calling": {"name": "at"},
            "args": [
                {"name": "x"},
                {"literal": "y"}
            ]
        },
        {"literal": "z"}
    ]
}
```

```
# Explicit string indexing with .
x."y"
>> {
    "calling": {"name": "at"},
    "args": [
        {"name": "x"},
        {"literal": "y"}
    ]
}
```

```
# Dynamic indexing with .
x.<<y>>
>> {
    "calling": {"name": "at"},
    "args": [
        {"name": "x"},
        {"name": "y"}
    ]
}
```

## Quoting and Unquoting

```
# Quoting
'(1)'
>> {"quote": {"literal": 1}}
```

```
# Unquoting
<<1>>
>> {"unquote": {"literal": 1}}
```

```
# Nested quoting and unquoting
'('(<<<<1>>>>)')'
>> {"quote": {"quote": {"unquote": {"unquote": {"literal": 1}}}}}
```

## Scopes

```
# Simple declaration
foo = 42; foo
>> {
    "defining": {
        "foo": {"literal": 42}
    },
    "result": {"name": "foo"}
}
```

```
# Nested scopes
foo = (bar = 1; bar); foo
>> {
    "defining": {
        "foo": {
            "defining": {
                "bar": {"literal": 1}
            },
            "result": {"name": "bar"}
        }
    },
    "result": {"name": "foo"}
}
```

```
# Array destructuring declaration
[foo, bar] = [42, 97];
[spam, eggs] = [216, 729];
plus(foo, bar, spam, eggs)
>> {
    "defining": {
        "#array1": {
            "array": [
                {"literal": 42},
                {"literal": 97}
            ]
        },
        "foo": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array1"},
                {"literal": 1}
            ]
        },
        "bar": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array1"},
                {"literal": 2}
            ]
        },
        "#array2": {
            "array": [
                {"literal": 216},
                {"literal": 729}
            ]
        },
        "spam": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array2"},
                {"literal": 1}
            ]
        },
        "eggs": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array2"},
                {"literal": 2}
            ]
        }
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [
            {"name": "foo"},
            {"name": "bar"},
            {"name": "spam"},
            {"name": "eggs"}
        ]
    }
}
```

```
# Nested array destructuring
[foo, [spam, eggs]] = [42, [97, 216]];
plus(foo, spam, eggs)
>> {
    "defining": {
        "#array1": {
            "array": [
                {"literal": 42},
                {"array": [
                    {"literal": 97},
                    {"literal": 216}
                ]}
            ]
        },
        "foo": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array1"},
                {"literal": 1}
            ]
        },
        "#array2": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array1"},
                {"literal": 2}
            ]
        },
        "spam": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array2"},
                {"literal": 1}
            ]
        },
        "eggs": {
            "calling": {"name": "at"},
            "args": [
                {"name": "#array2"},
                {"literal": 2}
            ]
        }
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [
            {"name": "foo"},
            {"name": "spam"},
            {"name": "eggs"}
        ]
    }
}
```
