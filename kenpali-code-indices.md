# Kenpali Code Indexing Specification

The tests here verify that the Kenpali code parser assigns the correct start and end indices to the nodes it produces.

## Literals|literals

```
# Keyword literal
null
>> {"type": "literal", "value": null, "start": 1, "end": 4}
```

```
# Number literal
1.23e4
>> {"type": "literal", "value": 1.23e4, "start": 1, "end": 6}
```

```
# String literal
"foobar"
>> {"type": "literal", "value": "foobar", "start": 1, "end": 8}
```

## Comments|comments

```
# Comment
// A billion-dollar mistake
null
>> {"type": "literal", "value": null, "start": 29, "end": 32}
```

## Names|names

```
# Name
foo
>> {"type": "name", "name": "foo", "start": 1, "end": 3}
```

```
# Name in a module
foo/bar
>> {"type": "name", "name": "bar", "from": "foo", "start": 1, "end": 7}
```

## Arrays|arrays

```
# Empty array
[]
>> {"type": "array", "elements": [], "start": 1, "end": 2}
```

```
# Array on one line
[1, 2, 3]
>> {
    "type": "array",
    "elements": [
        {"type": "literal", "value": 1, "start": 2, "end": 2},
        {"type": "literal", "value": 2, "start": 5, "end": 5},
        {"type": "literal", "value": 3, "start": 8, "end": 8}
    ],
    "start": 1,
    "end": 9
}
```

```
# Array spanning multiple lines
[
    1,
    2,
    3,
]
>> {
    "type": "array",
    "elements": [   
        {"type": "literal", "value": 1, "start": 7, "end": 7},
        {"type": "literal", "value": 2, "start": 14, "end": 14},
        {"type": "literal", "value": 3, "start": 21, "end": 21}
    ],
    "start": 1,
    "end": 24
}
```

```
# Array with spread
[42, *foo, 97]
>> {
    "type": "array",
    "elements": [
        {"type": "literal", "value": 42, "start": 2, "end": 3},
        {
            "type": "spread",
            "value": {
                "type": "name",
                "name": "foo",
                "start": 7,
                "end": 9
            },
            "start": 6, "end": 9
        },
        {"type": "literal", "value": 97, "start": 12, "end": 13}
    ],
    "start": 1,
    "end": 14
}
```

## Scopes|scopes

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
                    {"type": "ignore", "start": 2, "end": 2},
                    {"type": "ignore", "start": 5, "end": 5},
                    {"type": "name", "name": "foo", "start": 8, "end": 10}
                ],
                "start": 1,
                "end": 11
            },
            {"type": "name", "name": "arr", "start": 15, "end": 17}
        ]
    ],
    "result": {"type": "name", "name": "foo", "start": 20, "end": 22},
    "start": 1,
    "end": 22
}
```
