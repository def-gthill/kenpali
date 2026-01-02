# Kenpali Code Error-Checking Specification

The tests here verify that the Kenpali code parser:

- Rejects invalid code.
- Provides enough information about the exact error context to enable a helpful error message.

## Tokens|tokens

```
# Invalid character
25%
!! invalidCharacter {"character": "%", "start": 3, "end": 3}
```

## Literals|literals

```
# Invalid escape sequence
[42, "foo\x"]
!! invalidEscapeSequence {"value": "\\x", "start": 10, "end": 11}
```

```
# Unclosed Unicode escape sequence
[42, "foo\u{1f61b"]
!! unclosedUnicodeEscapeSequence {"value": "\\u{1f61b", "start": 10, "end": 17}
```

```
# Unclosed string literal
"foo
!! unclosedStringLiteral {"value": "\"foo", "start": 1, "end": 4}
```

```
# Unclosed raw string literal
`foo
!! unclosedStringLiteral {"value": "`foo", "start": 1, "end": 4}
```

## Scopes|scopes

```
# Underscore at the top level
_
!! ignoreAsExpression {"start": 1, "end": 1}
```

```
# Assignment at the top level
foo = 42
!! assignmentAsExpression {"start": 1, "end": 8}
```

```
# Chained assignment
foo = bar = 42; foo
!! assignmentAsExpression {"start": 7, "end": 14}
```
