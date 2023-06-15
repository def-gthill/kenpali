# Kenpali Code Specification

## Literals

```
# Literal null
null
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
# Calling a function call
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
        "params": ["x", ["y", {"literal": 3}]]
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
        "namedParams": [["y", {"literal": 3}]]
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
