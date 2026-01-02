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

`( ) [ ] { } , ; : => = |. | @ . $ ** * _ /`

All spaces, tabs, carriage returns, and linefeeds are considered whitespace and discarded.

Comments consist of the characters `//` and all following text until the end of the line. They are also discarded when parsing.

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

String literals can have any of the escape sequences that are valid in JSON.

```
# Literal string with escapes
"\n\t\r\b\f\"\\\/"
>> {"type": "literal", "value": "\n\t\r\b\f\"\\\/"}
```

In addition to standard JSON string escapes, Kenpali supports 5- and 6-digit Unicode escape sequences using the syntax `\u{<code>}`.

```
# Long Unicode escape sequence
"\u{1f61b}"
>> {"type": "literal", "value": "😛"}
```

Kenpali supports "raw string" syntax, delimited using backticks instead of quotes. Raw strings treat all backslashes as literal backslashes, rather than creating escape sequences, which can make backslash-heavy strings (e.g. regexes) easier to write and read. Raw strings parse to ordinary literal expressions.

```
# Raw literal string
`f\o\o\b\a\r`
>> {"type": "literal", "value": "f\\o\\o\\b\\a\\r"}
```

## Comments|comments

A comment can appear on its own line or at the end of a line.

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
# Name starting with a keyword
// Overeager lexers might mistake this for a keyword!
trueLove
>> {"type": "name", "name": "trueLove"}
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
# Array containing an expression to evaluate
[foo]
>> {"type": "array", "elements": [{"type": "name", "name": "foo"}]}
```

An array can optionally have a comma after the last element, though this is normally only done if the array spans multiple lines.

```
# Trailing comma
[
    1,
    2,
    3,
]
>> {
    "type": "array",
    "elements": [
        {"type": "literal", "value": 1},
        {"type": "literal", "value": 2},
        {"type": "literal", "value": 3}
    ]
}
```

Any of the array's elements can be _spreads_ instead of normal expressions, indicated by a `*` prefix. These are parsed into spread nodes in the JSON representation.

```
# Array with spread
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

`object_element ::= object_entry | object_key_name | object_spread`

`object_entry ::= assignable ":" assignable`

`object_key_name ::= NAME ":"`

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
```

If a key is a valid Kenpali name, the quotes can be omitted.

```
# Object with shorthand keys
{foo: "bar", spam: "eggs"}
>> {
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
```

If the key is meant to actually reference a name from the surrounding scope, enclose it in parentheses.

```
# Object with expression keys and values
{(key): value}
>> {
    "type": "object",
    "entries": [
        [
            {"type": "name", "name": "key"},
            {"type": "name", "name": "value"}
        ]
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
        [
            {"type": "literal", "value": "foo"},
            {"type": "name", "name": "foo"}
        ],
        [
            {"type": "literal", "value": "spam"},
            {"type": "name", "name": "spam"}
        ]
    ]
}
```

Any of the object's entries can be a _spread_ instead of a normal entry, indicated by a `**` prefix. These are parsed into entries with a `{"type": "spread"}` key in the JSON representation.

```
# Objects with spread
{answer: 42, **foo, question: 69}
>> {
    "type": "object",
    "entries": [
        [
            {"type": "literal", "value": "answer"},
            {"type": "literal", "value": 42}
        ],
        [
            {"type": "spread"},
            {"type": "name", "name": "foo"}
        ],
        [
            {"type": "literal", "value": "question"},
            {"type": "literal", "value": 69}
        ]
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

`expression ::= scope`

`scope ::= statement* assignable`

`statement ::= [name_pattern "="] assignable ";"`

`name_pattern ::= NAME | ignore | array_pattern | object_pattern`

`ignore ::= "_"`

`array_pattern ::= "[" [array_pattern_element ("," array_pattern_element)* [","]] "]"`

`array_pattern_element ::= name_pattern ["=" assignable] | array_rest`

`array_rest ::= "*" name_pattern`

`object_pattern ::= "{" [object_pattern_element ("," object_pattern_element)* [","]] "}"`

`object_pattern_element ::= object_pattern_simple ["=" assignable] | object_rest`

`object_pattern_simple ::= object_pattern_entry | object_pattern_key_name`

`object_pattern_entry ::= assignable ":" name_pattern`

`object_pattern_key_name ::= NAME ":"`

`object_rest ::= "**" name_pattern`

A scope parses to a [block expression](/docs/json#names)

In its simplest form, a scope is a list of assignments followed by an expression to evaluate, separated by semicolons.

```
# Simple declaration
foo = 42; bar = foo; bar
>> {
    "type": "block",
    "defs": [
        [
            {"type": "name", "name": "foo"},
            {"type": "literal", "value": 42}
        ],
        [
            {"type": "name", "name": "bar"},
            {"type": "name", "name": "foo"}
        ]
    ],
    "result": {"type": "name", "name": "bar"}
}
```

The semicolon operator has the lowest precedence, so blocks typically need to be enclosed in parentheses when nested in other expressions.

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

The left-hand side of an assignment can be a pattern instead of a single name.

An array pattern has a similar syntax to an array, and parses to an array pattern node in the JSON representation.

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

In an array pattern, elements can be ignored by using an underscore instead of a name. The underscore is parsed as an ignore node in the JSON representation. Since the underscore is special syntax, rather than a valid name with a conventional meaning, it can be used repeatedly in the same scope.

```
# Array destructuring with ignores
[_, _, foo] = arr; foo
>> {
    "type": "block",
    "defs": [
        [
            {
                "type": "arrayPattern",
                "names": [
                    {"type": "ignore"},
                    {"type": "ignore"},
                    {"type": "name", "name": "foo"}
                ]
            },
            {"type": "name", "name": "arr"}
        ]
    ],
    "result": {"type": "name", "name": "foo"}
}
```

An object pattern has a similar syntax to an object, and parses to an object pattern node in the JSON representation. The names to the left of the colon are the keys to look up in the object, with the name pattern to bind them to appearing on the right.

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

If the name pattern to the right of the colon is omitted, the value is assigned to the same name in the current scope: `{foo:}` is equivalent to `{foo: foo}`.

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

An expression can be used directly as a statement. This is equivalent to assigning to `_`; the resulting JSON has an ignore node as the assignment target, so the expression is evaluated and its result is discarded.

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

## Tight Pipelines|tight-pipelines

`tight_pipeline_call ::= atomic [tight_pipeline]`

`atomic ::= group | array | object | literal | name`

`tight_pipeline ::= tight_pipeline_step*`

`tight_pipeline_step ::= argument_list | property_access`

`argument_list ::= "(" [argument ("," argument)* [","]] ")"`

`argument ::= object_element | array_element`

`property_access ::= "." NAME`

Kenpali Code syntax relies heavily on _pipelines_—sequences of operations where the output from each operation is the input to the next. There are two kinds of pipelines, called _tight_ and _loose_ to reflect their different precedence levels. This section describes tight pipelines, which consist of function calls and property accesses.

### Function Calls|function-calls

Function call steps parse to [call expressions](/docs/json#functions).

```
# No arguments
foo()
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"}
}
```

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

Function arguments can contain any syntax that works in an [array](#arrays), including spreads.

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

Named arguments are passed using object-like syntax, and the same shorthand syntax available in [objects](#objects) is also available when passing named arguments.

```
# One named argument
foo(bar: 1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [
        [
            {"type": "literal", "value": "bar"},
            {"type": "literal", "value": 1}
        ]
    ]
}
```

```
# Two named arguments
foo(bar: 1, baz: 2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [
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
```

```
# Named argument with literal name
foo("bar": 1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [
        [
            {"type": "literal", "value": "bar"},
            {"type": "literal", "value": 1}
        ]
    ]
}
```

```
# Named argument with expression name
foo((bar): 1)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [
        [
            {"type": "name", "name": "bar"},
            {"type": "literal", "value": 1}
        ]
    ]
}
```

```
# Named arguments from names
foo(bar:, baz:)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [
        [
            {"type": "literal", "value": "bar"},
            {"type": "name", "name": "bar"}
        ],
        [
            {"type": "literal", "value": "baz"},
            {"type": "name", "name": "baz"}
        ]
    ]
}
```

```
# Spread named arguments
foo(**bar)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "namedArgs": [
        [
            {"type": "spread"},
            {"type": "name", "name": "bar"}
        ]
    ]
}
```

```
# Positional and named arguments
foo(1, 2, bar: 3, baz: 4)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "literal", "value": 1}, {"type": "literal", "value": 2}],
    "namedArgs": [
        [
            {"type": "literal", "value": "bar"},
            {"type": "literal", "value": 3}
        ],
        [
            {"type": "literal", "value": "baz"},
            {"type": "literal", "value": 4}
        ]
    ]
}
```

A tight pipeline can contain several function calls in a row, with the result of each call itself being called with the next arguments.

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

### Property Access|property-access

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

A tight pipeline can have any combination of function calls and property accesses.

```
# Complicated tight pipeline
foo.bar(baz)(qux).quux
>> {
    "type": "index",
    "collection": {
        "type": "call",
        "callee": {
            "type": "call",
            "callee": {
                "type": "index",
                "collection": {"type": "name", "name": "foo"},
                "index": {"type": "literal", "value": "bar"}
            },
            "posArgs": [{"type": "name", "name": "baz"}]
        },
        "posArgs": [{"type": "name", "name": "qux"}]
    },
    "index": {"type": "literal", "value": "quux"}
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

Positional parameters support all the same syntax available in [array patterns](#scopes), including optional and rest elements.

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

Named parameters support all the same syntax available in [object patterns](#scopes), including optional elements, rest elements, and recursively binding to a name pattern.

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
        [
            {"type": "rest"},
            {"type": "name", "name": "namedArgs"}
        ]
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

The body of a function is often a [scope](#scopes), which must be enclosed in parentheses because the semicolon has a lower precedence than the arrow.

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

## Loose Pipelines|loose-pipelines

`assignable ::= arrow_function | loose_pipeline_call | loose_pipeline | constant_function`

`loose_pipeline_call ::= tight_pipeline_call [loose_pipeline]`

`loose_pipeline ::= loose_pipeline_step*`

`loose_pipeline_step ::= pipe | pipe_dot | at`

`pipe ::= "|" tight_pipeline_call`

`pipe_dot ::= "|." name (tight_pipeline_step)*`

`at ::= "@" tight_pipeline_call`

Loose pipelines have a lower precedence than tight pipelines, and consist of pipes, loose property accesses, and indexing steps.

### Pipes|forward-pipe

Pipe steps are transformed into ordinary function calls, producing [call expressions](/docs/json#functions).

```
# Forward pipe into a bare name
1 | foo
>> {
    "type": "call",
    "callee": {"type": "name", "name": "foo"},
    "posArgs": [{"type": "literal", "value": 1}]
}
```

If the target of the pipe is a tight pipeline ending in a function call, the input is injected as the first positional argument to that call.

```
# Forward pipe injecting a first argument
1 | bar(2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "bar"},
    "posArgs": [{"type": "literal", "value": 1}, {"type": "literal", "value": 2}]
}
```

Any named arguments in the call are retained alongside the injected positional argument.

```
# Forward pipe injecting alongside a named argument
1 | bar(foo: 2)
>> {
    "type": "call",
    "callee": {"type": "name", "name": "bar"},
    "posArgs": [{"type": "literal", "value": 1}],
    "namedArgs": [
        [
            {"type": "literal", "value": "foo"},
            {"type": "literal", "value": 2}
        ]
    ]
}
```

Pipes can be chained together, with the output of each pipe becoming the input to the next.

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

Argument injection can be blocked by enclosing the target in parentheses.

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

The body of a function can be a loose pipeline without any parentheses—pipeline operators have higher precedence than the arrow.

```
# Pipe in an arrow
(x) => x | add(3)
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "x"}],
    "body": {
        "type": "call",
        "callee": {"type": "name", "name": "add"},
        "posArgs": [{"type": "name", "name": "x"}, {"type": "literal", "value": 3}]
    }
}
```

This precedence also means that a function inside a loose pipeline must be enclosed in parentheses.

```
# Function in a loose pipeline
1 | ((x) => y | foo(x))
>> {
    "type": "call",
    "callee": {
        "type": "function",
        "posParams": [{"type": "name", "name": "x"}],
        "body": {
            "type": "call",
            "callee": {"type": "name", "name": "foo"},
            "posArgs": [
                {"type": "name", "name": "y"},
                {"type": "name", "name": "x"}
            ]
        }
    },
    "posArgs": [{"type": "literal", "value": 1}]
}
```

### Loose property access|loose-property-access

The loose property access operator `|.` does the same thing as the tight property access operator `.`, but its precedence is that of a loose pipeline. If tight property access is used alongside loose pipeline operators, the tight property access happens first.

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

Using loose property access makes the operations happen from left to right instead.

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

Further tight pipeline steps can be chained onto the end of a loose property access; the whole sequence of operations happens from left to right.

```
# Tight pipeline hanging off a loose property access
x | y |.foo(bar)(baz).qux
>> {
    "type": "index",
    "collection": {
        "type": "call",
        "callee": {
            "type": "call",
            "callee": {
                "type": "index",
                "collection": {
                    "type": "call",
                    "callee": {"type": "name", "name": "y"},
                    "posArgs": [{"type": "name", "name": "x"}]
                },
                "index": {"type": "literal", "value": "foo"}
            },
            "posArgs": [{"type": "name", "name": "bar"}]
        },
        "posArgs": [{"type": "name", "name": "baz"}]
    },
    "index": {"type": "literal", "value": "qux"}
}
```

Compare this to the case where the loose pipeline uses only pipes.

```
# Piping into a complex tight pipeline
x | y | foo(bar)(baz).qux
>> {
    "type": "call",
    "callee": {
        "type": "index",
        "collection": {
            "type": "call",
            "callee": {
                "type": "call",
                "callee": {"type": "name", "name": "foo"},
                "posArgs": [{"type": "name", "name": "bar"}]
            },
            "posArgs": [{"type": "name", "name": "baz"}]
        },
        "index": {"type": "literal", "value": "qux"}
    },
    "posArgs": [
        {
            "type": "call",
            "callee": {"type": "name", "name": "y"},
            "posArgs": [{"type": "name", "name": "x"}]
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

Indexing steps can be used alongside pipes, with the operations happening from left to right.

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

Like other loose pipeline operators, indexing has a lower precedence than tight pipeline operators.

```
# Indexing with a method call result
x @ y.z()
>> {
    "type": "index",
    "collection": {"type": "name", "name": "x"},
    "index": {
        "type": "call",
        "callee": {
            "type": "index",
            "collection": {"type": "name", "name": "y"},
            "index": {"type": "literal", "value": "z"}
        }
    }
}
```

## Function Definition Shorthand|function-shorthand

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

As with ordinary functions, the body of a constant function can be a loose pipeline without the need for parentheses.

```
# Constant function shorthand with a pipe
$ foo | bar
>> {
    "type": "function",
    "body": {
        "type": "call",
        "callee": {"type": "name", "name": "bar"},
        "posArgs": [{"type": "name", "name": "foo"}]
    }
}
```

```
# Constant function shorthand with pipe-dot
$ foo |.bar
>> {
    "type": "function",
    "body": {
        "type": "index",
        "collection": {"type": "name", "name": "foo"},
        "index": {"type": "literal", "value": "bar"}
    }
}
```

A _point-free pipeline_ is written as a loose pipeline missing the initial value. It parses to a function with one positional parameter, which becomes the missing initial value.

`point_free_pipeline ::= loose_pipeline`

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

Point-free pipelines can start with any loose pipeline operator.

```
# Point-free pipeline starting with |.
|.foo(bar)(baz).qux
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "pipelineArg"}],
    "body": {
        "type": "index",
        "collection": {
            "type": "call",
            "callee": {
                "type": "call",
                "callee": {
                    "type": "index",
                    "collection": {"type": "name", "name": "pipelineArg"},
                    "index": {"type": "literal", "value": "foo"}
                },
                "posArgs": [{"type": "name", "name": "bar"}]
            },
            "posArgs": [{"type": "name", "name": "baz"}]
        },
        "index": {"type": "literal", "value": "qux"}
    }
}
```

```
# Point-free pipeline starting with @
@ foo | bar(2)
>> {
    "type": "function",
    "posParams": [{"type": "name", "name": "pipelineArg"}],
    "body": {
        "type": "call",
        "callee": {"type": "name", "name": "bar"},
        "posArgs": [
            {
                "type": "index",
                "collection": {"type": "name", "name": "pipelineArg"},
                "index": {"type": "name", "name": "foo"}
            },
            {"type": "literal", "value": 2}
        ]
    }
}
```
