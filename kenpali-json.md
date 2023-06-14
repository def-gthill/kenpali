# Kenpali JSON Specification

## Literals

A literal expression has the form `{"literal": <value>}`. The value can be `null`, `true`, `false`, or [any valid JSON string or number](https://www.json.org/json-en.html).

Examples:

```
# Literal null
{"literal": null}
>> null
```

```
# Literal false
{"literal": false}
>> false
```

```
# Literal true
{"literal": true}
>> true
```

```
# Literal number
{"literal": 1}
>> 1
```

```
# Literal string
{"literal": "foobar"}
>> "foobar"
```

## Arrays

```
# Array of literals
{"array": [{"literal": 1}, {"literal": 2}, {"literal": 3}]}
>> [1, 2, 3]
```

## Objects

```
# Object with literal values
{
    "object": [
        ["foo", {"literal": "bar"}],
        ["spam", {"literal": "eggs"}]
    ]
}
>> {foo: "bar", spam: "eggs"}
```

## Names

```
# Name binding
{
    "defining": {"foo": {"literal": 42}},
    "result": {"name": "foo"}
}
>> 42
```
