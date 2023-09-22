# Kenpali Code Specification

## Literals

```
# Literal null
null
>> {"literal": null}
```

## Arrays

## Objects

## Names

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
# One named argument
foo(bar: 1)
>> {
    "calling": {"name": "foo"},
    "namedArgs": {"bar": {"literal": 1}}
}
```

```
# Two named arguments
foo(bar: 1, baz: 2)
>> {
    "calling": {"name": "foo"},
    "namedArgs": {"bar": {"literal": 1}, "baz": {"literal": 2}}
}
```

```
# Positional and named arguments
foo(1, 2, bar: 3, baz: 4)
>> {
    "calling": {"name": "foo"},
    "args": [{"literal": 1}, {"literal": 2}],
    "namedArgs": {"bar": {"literal": 3}, "baz": {"literal": 4}}
}
```

```
# Optional arguments
foo(1, 2?, bar: 3, baz: 4?)
>> {
    "calling": {"name": "foo"},
    "args": [{"literal": 1}, {"optional": {"literal": 2}}],
    "namedArgs": {"bar": {"literal": 3}, "baz": {"optional": {"literal": 4}}}
}
```

```
# Error-passing arguments
foo(1, 2!, 3!?, bar: 4, baz: 5!, bax: 6!?)
>> {
    "calling": {"name": "foo"},
    "args": [
        {"literal": 1},
        {"errorPassing": {"literal": 2}},
        {"optional": {"errorPassing": {"literal": 3}}}
    ],
    "namedArgs": {
        "bar": {"literal": 4},
        "baz": {"errorPassing": {"literal": 5}},
        "bax": {"optional": {"errorPassing": {"literal": 6}}}
    }
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

## Function Definitions

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
# Correct precedence of .
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
