# Kenpali Code Error-Checking Specification

The tests here verify that the Kenpali code parser:

- Rejects invalid code.
- Provides enough information about the exact error context to enable a helpful error message.

## Tokens|tokens

```
# Invalid character
25%
!! invalidCharacter {"character": "%", start: {line: 1, column: 3}, end: {line: 1, column: 3}}
```

## Literals|literals

```
# Invalid escape sequence
"\x"
!! invalidEscapeSequence {"value": "\"\\x\"", start: {line: 1, column: 2}, end: {line: 1, column: 3}}
```

```
# Invalid Unicode escape sequence
"foo\u{1f61b"
!! unclosedUnicodeEscapeSequence {"value": "\"foo\\u{1f61b\"", start: {line: 1, column: 7}, end: {line: 1, column: 12}}
```

```
# Unclosed string literal
"foo
!! unclosedStringLiteral {"value": "\"foo", start: {line: 1, column: 1}, end: {line: 1, column: 4}}
```

```
# Unclosed raw string literal
`foo
!! unclosedStringLiteral {"value": "`foo", start: {line: 1, column: 1}, end: {line: 1, column: 4}}
```

## Scopes|scopes

```
# Underscore at the top level
_
!! ignoreAsExpression {start: {line: 1, column: 1}, end: {line: 1, column: 1}}
```

```
# Assignment as the scope result
foo = 42
!! assignmentAsExpression {start: {line: 1, column: 1}, end: {line: 1, column: 8}}
```

```
# Chained assignment
foo = bar = 42; foo
!! assignmentAsExpression {start: {line: 1, column: 7}, end: {line: 1, column: 14}}
```
