## Pure Validation

Even without defining any names, `bind` can be used for validation. The resulting bindings object has one key, `all`, that equals the original value if the validation succeeded and an error object if it failed.

```
# Successful validation of a simple value
42 | bind("number")
>> {all: 42}
```

```
# Failed validation of a simple value
"foo" | bind("number") @ "all"
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Successful validation of the "any" type
"foo" | bind("any")
>> {all: "foo"}
```

```
# Successful validation of the "object" type
{foo: 1, bar: 2} | bind("object")
>> {all: {foo: 1, bar: 2}}
```

```
# Failed validation of the "object" type
"foo" | bind("object") @ "all"
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Successful validation of the "function" type
typeOf | bind("function")
>> {all: typeOf}
```

```
# Failed validation of the "function" type
"foo" | bind("function") @ "all"
!! wrongType {"value": "foo", "expectedType": "function"}
```

```
# Successful validation of the "sequence" type
[1, 2, 3] | bind("sequence")
>> {all: [1, 2, 3]}
```

```
# Failed validation of the "sequence" type
42 | bind("sequence") @ "all"
!! wrongType {"value": 42, "expectedType": "sequence"}
```

```
# Successful validation of a type with a predicate
1 | bind(is("number", where: (n) => isLessThan(n, 10)))
>> {all: 1}
```

```
# Failed validation of a type with a predicate - wrong type
"foo" | bind(is("number", where: (n) => isLessThan(n, 10))) @ "all"
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Failed validation of a type with a predicate - false predicate
42 | bind(is("number", where: (n) => isLessThan(n, 10))) @ "all"
!! badValue {"value": 42}
```

```
# Successful validation of specific values
"green" | bind(oneOf("red", "green", "blue"))
>> {all: "green"}
```

```
# Failed validation of specific values
"foo" | bind(oneOf("red", "green", "blue")) @ "all"
!! badValue {"value": "foo", "options": ["red", "green", "blue"]}
```

```
# Successful validation of a union
"foo" | bind(either("string", "number"))
>> {all: "foo"}
```

```
# Failed validation of a union
null | bind(either("string", "number")) @ "all"
!! wrongType {"value": null}
```

```
# Successful validation of a uniform array
["foo", "bar"] | bind(arrayOf("string"))
>> {all: ["foo", "bar"]}
```

```
# Failed validation of a uniform array - not an array
"foo" | bind(arrayOf("string")) @ "all"
!! wrongType {"value": "foo", "expectedType": "array"}
```

```
# Failed validation of a uniform array - wrong element type
["foo", 1] | bind(arrayOf("string")) @ "all"
!! badElement {"value": ["foo", 1], "index": 2}
```

```
# Successful validation of an array schema
["foo", 42] | bind(["string", "number"])
>> {all: ["foo", 42]}
```

```
# Failed validation of an array schema - not an array
42 | bind(["string", "number"]) @ "all"
!! wrongType {"value": 42, "expectedType": "array"}
```

```
# Failed validation of an array schema - too short
["foo"] | bind(["string", "number"]) @ "all"
!! missingElement {"value": ["foo"], "schema": ["string", "number"]}
```

```
# Failed validation of an array schema - wrong element type
["foo", "bar"] | bind(["string", "number"]) @ "all"
!! badElement {"value": ["foo", "bar"], "index": 2}
```

```
# Successful validation of a uniform object - values only
{x: 42} | bind(objectOf(values: "number"))
>> {all: {x: 42}}
```

```
# Successful validation of a uniform object - keys and values
{x: 42} | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
>> {all: {x: 42}}
```

```
# Failed validation of a uniform object - not an object
"foo" | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number")) @ "all"
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Failed validation of a uniform object - bad key
{za: 42} | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number")) @ "all"
!! badKey {"key": "za"}
```

```
# Failed validation of a uniform object - bad property value
{z: "foo"} | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number")) @ "all"
!! badProperty {"key": "z", "value": "foo"}
```

## Automatic Binding

```
# Automatic binding of object properties
{name: "John", age: 42, job: "doctor"} | bind({name: "string", age: "number"})
>> {all: {name: "John", age: 42, job: "doctor"}, name: "John", age: 42}
```

```
# Failed object binding - not an object
"foo" | bind({name: "string", age: "number"}) @ "name"
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Failed object binding - missing key
{name: "John"} | bind({name: "string", age: "number"}) @ "age"
!! missingProperty {"value": {"name": "John"}, "key": "age"}
```

```
# Failed object binding - wrong property type
{name: "John", age: "middle"} | bind({name: "string", age: "number"}) @ "age"
!! badProperty {"value": {"name": "John", "age": "middle"}, "key": "age"}
```

```
# Lifting bindings out of an array
[{name: "John", age: 42}, {name: "Kate", age: 33}] | bind(arrayOf({name: "string", age: "number"}))
>> {all: [{name: "John", age: 42}, {name: "Kate", age: 33}], name: ["John", "Kate"], age: [42, 33]}
```

## Explicit Binding

```
# Explicit binding of a single value
42 | bind("number" | as("answer"))
>> {all: 42, answer: 42}
```

```
# Failed explicit binding of a single value
"foo" | bind("number" | as("answer")) @ "answer"
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Explicit and implicit binding
{name: "John", age: 42} | bind({name: "string", age: "number"} | as("person"))
>> {all: {name: "John", age: 42}, person: {name: "John", age: 42}, name: "John", age: 42}
```

```
# Explicit binding of array elements
["foo", 42] | bind(["string" | as("question"), "number" | as("answer")])
>> {all: ["foo", 42], question: "foo", answer: 42}
```

```
# Failed binding of array elements - missing element
["foo"] | bind(["string" | as("question"), "number" | as("answer")]) @ "answer"
!! missingElement {"value": ["foo"]}
```

```
# Explicit binding of uniform array elements
["foo", "bar"] | bind(arrayOf("string" | as("word")))
>> {all: ["foo", "bar"], word: ["foo", "bar"]}
```

```
# Explicit binding of uniform array elements on an empty array
[] | bind(arrayOf("string" | as("word")))
>> {all: [], word: []}
```

## Default Values

```
# Default value for missing array element
["foo"] | bind(["string" | as("question"), "number" | as("answer") | default(42)])
>> {all: ["foo"], question: "foo", answer: 42}
```

```
# Default value for present array element
["foo", 97] | bind(["string" | as("question"), "number" | as("answer") | default(42)])
>> {all: ["foo", 97], question: "foo", answer: 97}
```

```
# Default value for missing object property
{name: "John"} | bind({name: "string", age: "number" | default(30)})
>> {all: {name: "John"}, name: "John", age: 30}
```

```
# Default value for present object property
{name: "John", age: 42} | bind({name: "string", age: "number" | default(30)})
>> {all: {name: "John", age: 42}, name: "John", age: 42}
```

## Rest Bindings

```
# Rest binding for array elements
["foo", 42, 97] | bind(["string" | as("question"), rest("number") | as("answers")])
>> {all: ["foo", 42, 97], question: "foo", answers: [42, 97]}
```

```
# Empty rest binding for array elements
["foo"] | bind(["string" | as("question"), rest("number") | as("answers")])
>> {all: ["foo"], question: "foo", answers: []}
```

```
# Rest binding for object elements
{foo: "bar", x: 1, y: 2} | bind({foo: "string", vars: rest("number")})
>> {all: {foo: "bar", x: 1, y: 2}, foo: "bar", vars: {x: 1, y: 2}}
```

## Errors

```
# Error short-circuiting in type checking
plus("foo") | bind("string")
!! wrongArgumentType {"value": "foo"}
```

```
# Error short-circuiting in array schema
[plus("foo")] | bind(["any" | as("answer")]) @ "answer"
!! wrongArgumentType {"value": "foo"}
```

```
# Error short-circuiting in array rest binding
[plus("foo")] | bind([rest("any") | as("answers")]) @ "answers"
!! wrongArgumentType {"value": "foo"}
```

```
# Error short-circuiting in union schema
plus("foo") | bind(either("string", "number"))
!! wrongArgumentType {"value": "foo"}
```

## Switch

```
# Switch on type
foo = (x) => (
    x | switch(
        ["number", (n) => (n | plus(3))],
        ["string", (s) => (s | length)],
        ["boolean", (b) => if(b, then: 42, else: 86)],
    )
);
[
    foo(5),
    foo("bar"),
    foo(false),
    foo(true),
]
>> [8, 3, 86, 42]
```

```
# Switch with destructuring
heightInMetres = (height) => (
    height | switch(
        [
            {feet: "number", inches: "number"},
            (feet:, inches:) => (
                feet
                | times(12)
                | plus(inches)
                | times(13)
                | dividedBy(512)
            )
        ],
        [
            {cm: "number"},
            (cm:) => (cm | dividedBy(100))
        ]
    )
);
[
    heightInMetres({cm: 185}),
    heightInMetres({feet: 6, inches: 1}),
]
>> [1.85, 1.853515625]
```
