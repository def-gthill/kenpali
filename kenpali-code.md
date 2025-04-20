# Kenpali Code Specification

## Tokens|tokens

Kenpali code is first split into tokens.

The following regular expressions are used for number and string literals:

`NUMBER = -?(0|[1-9](\d*))(.\d+)?([Ee][+-]?\d+)?`

`STRING = "(\\"|[^"])*"`

``RAW_STRING = `[^`]*` ``

The following regular expression is used for names:

`NAME = [A-Za-z][A-Za-z0-9]*`

The keywords `null`, `false`, and `true` are treated as literal tokens even though they match the name pattern.

The following character sequences are tokens:

`( ) [ ] { } , ; : => = |. | @ . ! $ ** *`

All spaces, tabs, carriage returns, and linefeeds are considered whitespace and discarded.

Comments consist of the characters `//` and all following text until the end of the line. They are also discarded when parsing.

Any characters not matching the above token patterns cause parsing to fail.

## Literals|literals

`literal ::= "null" | "false" | "true" | NUMBER | STRING | RAW_STRING`

A literal parses to a [literal expression](/docs/json#literals).

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

Kenpali supports "raw string" syntax, delimited using backticks instead of quotes. Raw strings treat all backslashes as literal backslashes, rather than creating escape sequences, which can make backslash-heavy strings (e.g. regexes) easier to write and read. Raw strings parse to ordinary literal expressions.

```
# Raw literal string
`f\o\o\b\a\r`
>> {"literal": "f\\o\\o\\b\\a\\r"}
```

## Comments|comments

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

## Names|names

`name ::= [NAME "/"] NAME`

A name normally parses to a [name expression](/docs/json#names), though some other syntactic structures use names for other purposes.

The form with a slash indicates that the name is found in a module. The module name is added as the `from` property of the name expression.

Kenpali uses `camelCase` for names by convention.

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

```
# Name in a module
foo/bar
>> {"name": "bar", "from": "foo"}
```

## Arrays|arrays

`array ::= "[" [array_element ("," array_element)* [","]] "]"`

`array_element ::= assignable | array_spread`

`array_spread ::= "*" assignable`

An array parses to an [array expression](/docs/json#arrays).

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
[42, *foo, 97]
>> {
    "array": [
        {"literal": 42},
        {"spread": {"name": "foo"}},
        {"literal": 97}
    ]
}
```

## Objects|objects

`object ::= "{" [object_element ("," object_element)* [","]] "}"`

`object_element ::= assignable ":" assignable | name ":" | object_spread`

`object_spread ::= "**" assignable`

An object parses to an [object expression](/docs/json#objects).

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

If the key is meant to actually reference a name from the surrounding scope, enclose it in parentheses.

```
# Object with expression keys and values
{(key): value}
>> {"object": [[{"name": "key"}, {"name": "value"}]]}
```

If the value is omitted, it defaults to reading the property name from the surrounding scope: `{foo:}` is equivalent to `{foo: foo}`.

```
# Object taking properties from names
{foo:, spam:}
>> {
    "object": [
        ["foo", {"name": "foo"}],
        ["spam", {"name": "spam"}]
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
# Objects with spread
{answer: 42, **foo, question: 69}
>> {
    "object": [
        ["answer", {"literal": 42}],
        {"spread": {"name": "foo"}},
        ["question", {"literal": 69}]
    ]
}
```

## Groups|groups

`group ::= "(" expression ")"`

Any expression can be enclosed in parentheses to force it to be parsed first, overriding precedence rules and special processing rules. A group parses to the same JSON as the expression it contains would if parsed on its own.

```
# Groups
(foo)
>> {"name": "foo"}
```

## Scopes|scopes

`scope ::= statement* assignable`

`statement ::= [defining_pattern "="] assignable ";"`

`defining_pattern ::= name | array_pattern | object_pattern`

`array_pattern ::= "[" [array_pattern_element ("," array_pattern_element)* [","]] "]"`

`array_pattern_element ::= defining_pattern ["=" assignable] | "*" defining_pattern`

`object_pattern ::= "{" [object_pattern_element ("," object_pattern_element)* [","]] "}"`

`object_pattern_element ::= object_pattern_simple ["=" assignable] | "**" defining_pattern`

`object_pattern_simple ::= assignable ":" defining_pattern | name ":"`

A scope parses to a [defining expression](/docs/json#names)

```
# Simple declaration
foo = 42; foo
>> {
    "defining": [
        ["foo", {"literal": 42}]
    ],
    "result": {"name": "foo"}
}
```

```
# Nested scopes
foo = (bar = 1; bar); foo
>> {
    "defining": [
        [
            "foo",
            {
                "defining": [
                    ["bar", {"literal": 1}]
                ],
                "result": {"name": "bar"}
            }
        ]
    ],
    "result": {"name": "foo"}
}
```

```
# Array destructuring declaration
[foo, bar] = arr; foo
>> {
    "defining": [
        [
            {"arrayPattern": ["foo", "bar"]},
            {"name": "arr"}
        ]
    ],
    "result": {"name": "foo"}
}
```

```
# Nested array destructuring
[foo, [spam, eggs]] = arr; foo
>> {
    "defining": [
        [
            {
                "arrayPattern": [
                    "foo",
                    {"arrayPattern": ["spam", "eggs"]}
                ]
            },
            {"name": "arr"}
        ]
    ],
    "result": {"name": "foo"}
}
```

```
# Object destructuring declaration
{foo:, bar:} = obj; foo
>> {
    "defining": [
        [
            {"objectPattern": ["foo", "bar"]},
            {"name": "obj"}
        ]
    ],
    "result": {"name": "foo"}
}
```

```
# Object destructuring with aliases
{foo: spam, bar: eggs} = obj; spam
>> {
    "defining": [
        [
            {
                "objectPattern": [
                    {"name": "spam", "property": "foo"},
                    {"name": "eggs", "property": "bar"}
                ]
            },
            {"name": "obj"}
        ]
    ],
    "result": {"name": "spam"}
}
```

```
# Expression statements
foo; 42
>> {
    "defining": [
        [null, {"name": "foo"}]
    ],
    "result": {"literal": 42}
}
```

```
# Assignment as the scope result
foo = 42
!! missingStatementSeparator {"line": 1, "column": 9}
```

```
# Chained assignment
foo = bar = 42; foo
!! missingStatementSeparator {"line": 1, "column": 11}
```

## Property Access|property-access

`tight_pipeline ::= atomic ("." name)*`

`atomic ::= group | array | object | literal | name`

A single property can be extracted from an object by putting the property name after a dot. This parses to an [indexing expression](/docs/json#indexing).

```
# Tight-binding property access
foo.bar
>> {
    "indexing": {"name": "foo"},
    "at": {"literal": "bar"}
}
```

Property access can be chained, and associates left to right.

```
# Chained tight-binding property access
foo.bar.baz
>> {
    "indexing": {
        "indexing": {"name": "foo"},
        "at": {"literal": "bar"}
    },
    "at": {"literal": "baz"}
}
```

## Function Definitions|function-definitions

`arrow_function ::= parameter_list "=>" assignable`

`parameter_list ::= "(" [parameter ("," parameter)* [","]] ")"`

`parameter ::= array_pattern_element | object_pattern_element`

A function definition parses to a [given expression](/docs/json#functions).

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
        "params": [{"rest": "args"}]
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
        "namedParams": [{"rest": "namedArgs"}]
    },
    "result": {"name": "namedArgs"}
}
```

```
# Named parameter with alias
(x, y: z) => plus(x, z)
>> {
    "given": {
        "params": ["x"],
        "namedParams": [{"name": "z", "property": "y"}]
    },
    "result": {
        "calling": {"name": "plus"},
        "args": [{"name": "x"}, {"name": "z"}]
    }
}
```

```
# Array destructuring in parameters
([foo, bar]) => foo
>> {
    "given": {
        "params": [
            {"arrayPattern": ["foo", "bar"]}
        ]
    },
    "result": {"name": "foo"}
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
        "defining": [
            [
                "y",
                {
                    "calling": {"name": "plus"},
                    "args": [{"name": "x"}, {"literal": 3}]
                }
            ]
        ],
        "result": {"name": "y"}
    }
}
```

## Pipelines|pipelines

Most of Kenpali's operators are pipeline operators. All pipeline operators have the same precedence, and associate left to right.

`assignable ::= arrow_function | pipeline | point_free_pipeline | constant_function`

`pipeline ::= tight_pipeline pipeline_step*`

`pipeline_step ::= call | pipe | pipe_call | pipe_dot | at | "!"`

`call ::= argument_list`

`pipe ::= "|" tight_pipeline`

`pipe_call ::= "|" tight_pipeline argument_list`

`pipe_dot ::= "|." name`

`at ::= "@" tight_pipeline`

`argument_list ::= "(" [argument ("," argument)* [","]] ")"`

`argument ::= positional_argument | named_argument | array_spread | object_spread`

`positional_argument ::= assignable`

`named_argument ::= name ":" [assignable]`

### Function Calls|function-calls

Function call steps parse to [calling expressions](/docs/json#functions).

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
# Named arguments from names
foo(bar:, baz:)
>> {
    "calling": {"name": "foo"},
    "namedArgs": [["bar", {"name": "bar"}], ["baz", {"name": "baz"}]]
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

### Pipes and Pipe-Calls|forward-pipe

Pipe and pipe-call steps are transformed into ordinary function calls, producting [calling expressions](/docs/json#functions).

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

### Error Catching|error-catching

Error catching steps parse to [catching expressions](/docs/json#errors).

```
# Error catching
foo !
>> {
    "catching": {"name": "foo"}
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

### Indexing|indexing

Indexing steps parse to [indexing expressions](/docs/json#indexing).

```
# Indexing
["foo", "bar"] @ 2
>> {
    "indexing": {"array": [
        {"literal": "foo"},
        {"literal": "bar"}
    ]},
    "at": {"literal": 2}
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
                    "indexing": {"name": "x"},
                    "at": {"literal": 1}
                }
            ]
        },
        {
            "indexing": {
                "calling": {"name": "f"},
                "args": [{"name": "x"}]
            },
            "at": {"literal": 1}
        }
    ]
}
```

```
# Correct precedence of tight-binding property access
x | y.z
>> {
    "calling": {
        "indexing": {"name": "y"},
        "at": {"literal": "z"}
    },
    "args": [{"name": "x"}]
}
```

```
# Loose-binding property access
x | y |.z
>> {
    "indexing": {
        "calling": {"name": "y"},
        "args": [{"name": "x"}]
    },
    "at": {"literal": "z"}
}
```

```
# Indexing with an explicit string
x @ "y"
>> {
    "indexing": {"name": "x"},
    "at": {"literal": "y"}
}
```

## Function Definition Shorthand

Kenpali has two kinds of shorthand syntax for compactly defining common function types.

A _constant function_ ignores any arguments passed to it. The shorthand syntax is a `$` followed by an expression for the function's return value.

`constant_function ::= "$" assignable`

```
# Constant function shorthand
$ foo
>> {
    "given": {},
    "result": {"name": "foo"}
}
```

A _point-free pipeline_ is written as a pipeline missing the initial value. It parses to a function with one positional parameter, which becomes the missing initial value.

`point_free_pipeline ::= pipeline_step*`

```
# Point-free pipeline starting with |
| foo | bar(2)
>> {
    "given": {"params": ["pipelineArg"]},
    "result": {
        "calling": {"name": "bar"},
        "args": [
            {
                "calling": {"name": "foo"},
                "args": [{"name": "pipelineArg"}]
            },
            {"literal": 2}
        ]
    }
}
```
