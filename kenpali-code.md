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
>> {"type": "literal", "value": null}
```

```
# Literal false
false
>> {"type": "literal", "value": false}
```

```
# Literal true
true
>> {"type": "literal", "value": true}
```

```
# Literal integer
1
>> {"type": "literal", "value": 1}
```

```
# Literal decimal
-2.5
>> {"type": "literal", "value": -2.5}
```

```
# Literal decimal in scientific notation
1.23e4
>> {"type": "literal", "value": 1.23e4}
```

```
# Literal string
"foobar"
>> {"type": "literal", "value": "foobar"}
```

Kenpali supports "raw string" syntax, delimited using backticks instead of quotes. Raw strings treat all backslashes as literal backslashes, rather than creating escape sequences, which can make backslash-heavy strings (e.g. regexes) easier to write and read. Raw strings parse to ordinary literal expressions.

```
# Raw literal string
`f\o\o\b\a\r`
>> {"type": "literal", "value": "f\\o\\o\\b\\a\\r"}
```

## Comments|comments

```
# A comment on its own line
// A billion-dollar mistake
null
>> {"type": "literal", "value": null}
```

```
# A comment at the end of a line
null // A billion-dollar mistake
>> {"type": "literal", "value": null}
```

## Names|names

`name ::= [NAME "/"] NAME`

A name normally parses to a [name expression](/docs/json#names), though some other syntactic structures use names for other purposes.

The form with a slash indicates that the name is found in a module. The module name is added as the `from` property of the name expression.

Kenpali uses `camelCase` for names by convention.

```
# Name with only letters
foo
>> {"type": "name", "name": "foo"}
```

```
# Name with uppercase letters
FOO
>> {"type": "name", "name": "FOO"}
```

```
# Name with numbers
f00
>> {"type": "name", "name": "f00"}
```

```
# Name in a module
foo/bar
>> {"type": "name", "name": "bar", "from": "foo"}
```

## Arrays|arrays

`array ::= "[" [array_element ("," array_element)* [","]] "]"`

`array_element ::= assignable | array_spread`

`array_spread ::= "*" assignable`

An array parses to an [array expression](/docs/json#arrays).

```
# Empty array
[]
>> {"type": "array", "elements": []}
```

```
# Single-element array
[1]
>> {"type": "array", "elements": [{"type": "literal", "value": 1}]}
```

```
# Array of literals
[1, 2, 3]
>> {
    "type": "array",
    "elements": [
        {"type": "literal", "value": 1},
        {"type": "literal", "value": 2},
        {"type": "literal", "value": 3}
    ]
}
```

```
# Trailing comma
[1, 2, 3,]
>> {
    "type": "array",
    "elements": [
        {"type": "literal", "value": 1},
        {"type": "literal", "value": 2},
        {"type": "literal", "value": 3}
    ]
}
```

```
# Array containing an expression to evaluate
[foo]
>> {"type": "array", "elements": [{"type": "name", "name": "foo"}]}
```

```
# Arrays with spread
[42, *foo, 97]
>> {
    "type": "array",
    "elements": [
        {"type": "literal", "value": 42},
        {"type": "spread", "value": {"type": "name", "name": "foo"}},
        {"type": "literal", "value": 97}
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
>> {"type": "object", "entries": []}
```

```
# Object with literal values
{"foo": "bar", "spam": "eggs"}
>> {
    "type": "object",
    "entries": [
        ["foo", {"type": "literal", "value": "bar"}],
        ["spam", {"type": "literal", "value": "eggs"}]
    ]
}
```

If a key is a valid Kenpali name, the quotes can be omitted.

```
# Object with shorthand keys
{foo: "bar", spam: "eggs"}
>> {
    "type": "object",
    "entries": [
        ["foo", {"type": "literal", "value": "bar"}],
        ["spam", {"type": "literal", "value": "eggs"}]
    ]
}
```

If the key is meant to actually reference a name from the surrounding scope, enclose it in parentheses.

```
# Object with expression keys and values
{(key): value}
>> {
    "type": "object",
    "entries": [
        [{"type": "name", "name": "key"}, {"type": "name", "name": "value"}]
    ]
}
```

If the value is omitted, it defaults to reading the property name from the surrounding scope: `{foo:}` is equivalent to `{foo: foo}`.

```
# Object taking properties from names
{foo:, spam:}
>> {
    "type": "object",
    "entries": [
        ["foo", {"type": "name", "name": "foo"}],
        ["spam", {"type": "name", "name": "spam"}]
    ]
}
```

```
# Objects with spread
{answer: 42, **foo, question: 69}
>> {
    "type": "object",
    "entries": [
        ["answer", {"type": "literal", "value": 42}],
        {"type": "spread", "value": {"type": "name", "name": "foo"}},
        ["question", {"type": "literal", "value": 69}]
    ]
}
```

## Groups|groups

`group ::= "(" expression ")"`

Any expression can be enclosed in parentheses to force it to be parsed first, overriding precedence rules and special processing rules. A group parses to the same JSON as the expression it contains would if parsed on its own.

```
# Groups
(foo)
>> {"type": "name", "name": "foo"}
```

## Scopes|scopes

`scope ::= statement* assignable`

`statement ::= [name_pattern "="] assignable ";"`

`name_pattern ::= name | array_pattern | object_pattern`

`array_pattern ::= "[" [array_pattern_element ("," array_pattern_element)* [","]] "]"`

`array_pattern_element ::= name_pattern ["=" assignable] | "*" name_pattern`

`object_pattern ::= "{" [object_pattern_element ("," object_pattern_element)* [","]] "}"`

`object_pattern_element ::= object_pattern_simple ["=" assignable] | "**" name_pattern`

`object_pattern_simple ::= assignable ":" name_pattern | name ":"`

A scope parses to a [block expression](/docs/json#names)

```
# Simple declaration
foo = 42; foo
>> {
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {"type": "literal", "value": 42}
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
```

```
# Nested scopes
foo = (bar = 1; bar); foo
>> {
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {
                "type": "block",
                "defs": [
                    [
                        {"type": "name", "name": "bar"},
                        {"type": "literal", "value": 1}
                    ]
                ],
                "result": {"type": "name", "name": "bar"}
            }
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
```

```
# Array destructuring declaration
[foo, bar] = arr; foo
>> {
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
            {"type": "name", "name": "arr"}
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
```

```
# Object destructuring declaration
{foo:, bar:} = obj; foo
>> {
    "type": "block",
    "defs": [
        [
            {
                "type": "objectPattern",
                "entries": [
                    [
                        {"type": "literal", "value": "foo"},
                        {"type": "name", "name": "foo"}
                    ],
                    [
                        {"type": "literal", "value": "bar"},
                        {"type": "name", "name": "bar"}
                    ]
                ]
            },
            {"type": "name", "name": "obj"}
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
```

```
# Object destructuring with aliases
{foo: spam, bar: eggs} = obj; spam
>> {
    "type": "block",
    "defs": [
        [
            {
                "type": "objectPattern",
                "entries": [
                    [
                        {"type": "literal", "value": "foo"},
                        {"type": "name", "name": "spam"}
                    ],
                    [
                        {"type": "literal", "value": "bar"},
                        {"type": "name", "name": "eggs"}
                    ]
                ]
            },
            {"type": "name", "name": "obj"}
        ]
    ],
    "result": {"type": "name", "name": "spam"}
}
```

```
# Expression statements
foo; 42
>> {
    "type": "block",
    "defs": [
        [
            {"type": "ignore"},
            {"type": "name", "name": "foo"}
        ]
    ],
    "result": {"type": "literal", "value": 42}
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

A single property can be extracted from an object by putting the property name after a dot. This parses to an [index expression](/docs/json#indexing).

```
# Tight-binding property access
foo.bar
>> {
    "type": "index",
    "collection": {"type": "name", "name": "foo"},
    "index": {"type": "literal", "value": "bar"}
}
```

Property access can be chained, and associates left to right.

```
# Chained tight-binding property access
foo.bar.baz
>> {
    "type": "index",
    "collection": {
        "type": "index",
        "collection": {"type": "name", "name": "foo"},
        "index": {"type": "literal", "value": "bar"}
    },
    "index": {"type": "literal", "value": "baz"}
}
```

## Function Definitions|function-definitions

`arrow_function ::= parameter_list "=>" assignable`

`parameter_list ::= "(" [parameter ("," parameter)* [","]] ")"`

`parameter ::= array_pattern_element | object_pattern_element`

A function definition parses to a [function expression](/docs/json#functions).

```
# No parameters
() => 42
>> {"type": "function", "body": {"type": "literal", "value": 42}}
```

```
# One positional parameter
(x) => x
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "body": {"type": "name", "name": "x"}
}
```

```
# Optional positional parameter
(x, y = 3) => x
>> {
    "type": "function",
    "posParams": [
        {"type": "name", "name": "x"},
        {
            "type": "optional",
            "name": {"type": "name", "name": "y"},
            "defaultValue": {"type": "literal", "value": 3}
        }
    ],
    "body": {"type": "name", "name": "x"}
}
```

```
# Positional rest parameter
(*args) => args
>> {
    "type": "function",
    "posParams": [{"type": "rest", "name": {"type": "name", "name": "args"}}],
    "body": {"type": "name", "name": "args"}
}
```

```
# Named parameter
(x, y:) => x
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "namedParams": [
        [
            {"type": "literal", "value": "y"},
            {"type": "name", "name": "y"}
        ]
    ],
    "body": {"type": "name", "name": "x"}
}
```

```
# Optional named parameter
(x, y: = 3) => x
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "namedParams": [
        [
            {"type": "literal", "value": "y"},
            {
                "type": "optional",
                "name": {"type": "name", "name": "y"},
                "defaultValue": {"type": "literal", "value": 3}
            }
        ]
    ],
    "body": {"type": "name", "name": "x"}
}
```

```
# Named rest parameter
(**namedArgs) => namedArgs
>> {
    "type": "function",
    "namedParams": [
        {
            "type": "rest",
            "name": {"type": "name", "name": "namedArgs"}
        }
    ],
    "body": {"type": "name", "name": "namedArgs"}
}
```

```
# Named parameter with alias
(x, y: z) => x
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "namedParams": [
        [
            {"type": "literal", "value": "y"},
            {"type": "name", "name": "z"}
        ]
    ],
    "body": {"type": "name", "name": "x"}
}
```

```
# Scope in function body
(x) => (y = x; y)
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "body": {
        "type": "block",
        "defs": [
            [
                {"type": "name", "name": "y"},
                {"type": "name", "name": "x"}
            ]
        ],
        "result": {"type": "name", "name": "y"}
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

Function call steps parse to [call expressions](/docs/json#functions).

```
# One positional argument
foo(1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "literal", "value": 1}]
}
```

```
# Two positional arguments
foo(1, 2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "literal", "value": 1}, {"type": "literal", "value": 2}]
}
```

```
# Spread positional arguments
foo(*bar)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "spread", "value": {"type": "name", "name": "bar"}}]
}
```

```
# Positional and spread positional arguments
foo(1, *bar)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [
        {"type": "literal", "value": 1},
        {"type": "spread", "value": {"type": "name", "name": "bar"}}
    ]
}
```

```
# One named argument
foo(bar: 1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [["bar", {"type": "literal", "value": 1}]]
}
```

```
# Two named arguments
foo(bar: 1, baz: 2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [["bar", {"type": "literal", "value": 1}], ["baz", {"type": "literal", "value": 2}]]
}
```

```
# Named argument with literal name
foo("bar": 1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [["bar", {"type": "literal", "value": 1}]]
}
```

```
# Named argument with expression name
foo((bar): 1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [[{"type": "name", "name": "bar"}, {"type": "literal", "value": 1}]]
}
```

```
# Named arguments from names
foo(bar:, baz:)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [["bar", {"type": "name", "name": "bar"}], ["baz", {"type": "name", "name": "baz"}]]
}
```

```
# Spread named arguments
foo(**bar)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [{"type": "spread", "value": {"type": "name", "name": "bar"}}]
}
```

```
# Positional and named arguments
foo(1, 2, bar: 3, baz: 4)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "literal", "value": 1}, {"type": "literal", "value": 2}],
    "namedArgs": [["bar", {"type": "literal", "value": 3}], ["baz", {"type": "literal", "value": 4}]]
}
```

```
# Calling the result of a function call
foo(x)(y)
>> {
    "type": "call",
    "callee": {
        "type": "call",
        "callee": {"type": "name", "name": "foo"},
        "posArgs": [{"type": "name", "name": "x"}]
    },
    "posArgs": [{"type": "name", "name": "y"}]
}
```

### Pipes and Pipe-Calls|forward-pipe

Pipe and pipe-call steps are transformed into ordinary function calls, producing [call expressions](/docs/json#functions).

```
# Forward pipe into a bare name
1 | foo
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "literal", "value": 1}]
}
```

```
# Forward pipe injecting a first argument
1 | bar(2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "bar"},
    "posArgs": [{"type": "literal", "value": 1}, {"type": "literal", "value": 2}]
}
```

```
# Forward pipe injecting alongside a named argument
1 | bar(foo: 2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "bar"},
    "posArgs": [{"type": "literal", "value": 1}],
    "namedArgs": [["foo", {"type": "literal", "value": 2}]]
}
```

```
# Chaining forward pipes
1 | foo | bar(2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "bar"},
    "posArgs": [
        {
            "type": "call",
            "callee": {"type": "name", "name": "foo"},
            "posArgs": [{"type": "literal", "value": 1}]
        },
        {"type": "literal", "value": 2}
    ]
}
```

```
# Blocking first-argument injection
1 | (bar(2))
>> {
    "type": "call",
    "callee": {
        "type": "call",
        "callee": {"type": "name", "name": "bar"},
        "posArgs": [{"type": "literal", "value": 2}]
    },
    "posArgs": [{"type": "literal", "value": 1}]
}
```

```
# Pipe in an arrow
(x) => x | plus(3)
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "body": {
        "type": "call",
        "callee": {"type": "name", "name": "plus"},
        "posArgs": [{"type": "name", "name": "x"}, {"type": "literal", "value": 3}]
    }
}
```

### Error Catching|error-catching

Error catching steps parse to [catch expressions](/docs/json#errors).

```
# Error catching
foo !
>> {
    "type": "catch",
    "expression": {"type": "name", "name": "foo"}
}
```

```
# Error catching in pipeline
1 | foo ! | bar
>> {
    "type": "call",
    "callee": {"type": "name", "name": "bar"},
    "posArgs": [
        {
            "type": "catch",
            "expression": {
                "type": "call",
                "callee": {"type": "name", "name": "foo"},
                "posArgs": [{"type": "literal", "value": 1}]
            }
        }
    ]
}
```

### Indexing|indexing

Indexing steps parse to [index expressions](/docs/json#indexing).

```
# Indexing
["foo", "bar"] @ 2
>> {
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
```

```
# Correct precedence of @
[x @ 1 | f, x | f @ 1]
>> {
    "type": "array",
    "elements": [
        {
            "type": "call",
            "callee": {"type": "name", "name": "f"},
            "posArgs": [
                {
                    "type": "index",
                    "collection": {"type": "name", "name": "x"},
                    "index": {"type": "literal", "value": 1}
                }
            ]
        },
        {
            "type": "index",
            "collection": {
                "type": "call",
                "callee": {"type": "name", "name": "f"},
                "posArgs": [{"type": "name", "name": "x"}]
            },
            "index": {"type": "literal", "value": 1}
        }
    ]
}
```

```
# Correct precedence of tight-binding property access
x | y.z
>> {
    "type": "call",
    "callee": {
        "type": "index",
        "collection": {"type": "name", "name": "y"},
        "index": {"type": "literal", "value": "z"}
    },
    "posArgs": [{"type": "name", "name": "x"}]
}
```

```
# Loose-binding property access
x | y |.z
>> {
    "type": "index",
    "collection": {
        "type": "call",
        "callee": {"type": "name", "name": "y"},
        "posArgs": [{"type": "name", "name": "x"}]
    },
    "index": {"type": "literal", "value": "z"}
}
```

```
# Indexing with an explicit string
x @ "y"
>> {
    "type": "index",
    "collection": {"type": "name", "name": "x"},
    "index": {"type": "literal", "value": "y"}
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
    "type": "function",
    "body": {"type": "name", "name": "foo"}
}
```

A _point-free pipeline_ is written as a pipeline missing the initial value. It parses to a function with one positional parameter, which becomes the missing initial value.

`point_free_pipeline ::= pipeline_step*`

```
# Point-free pipeline starting with |
| foo | bar(2)
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "pipelineArg"}],
    "body": {
        "type": "call",
        "callee": {"type": "name", "name": "bar"},
        "posArgs": [
            {
                "type": "call",
                "callee": {"type": "name", "name": "foo"},
                "posArgs": [{"type": "name", "name": "pipelineArg"}]
            },
            {"type": "literal", "value": 2}
        ]
    }
}
```
