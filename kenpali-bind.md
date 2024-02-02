## Pure Validation

Even without defining any names, `bind` can be used for validation; it returns `{}` if the value is valid, and an error object otherwise.

```
# Successful validation of a simple value
42 | bind("number")
>> {}
```

```
# Failed validation of a simple value
"foo" | bind("number")
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Successful validation of the "any" type
"foo" | bind("any")
>> {}
```

```
# Successful validation of the "object" type
{foo: 1, bar: 2} | bind("object")
>> {}
```

```
# Failed validation of the "object" type
"foo" | bind("object")
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Successful validation of the "function" type
typeOf | bind("function")
>> {}
```

```
# Failed validation of the "function" type
"foo" | bind("function")
!! wrongType {"value": "foo", "expectedType": "function"}
```

```
# Successful validation of the "sequence" type
[1, 2, 3] | bind("sequence")
>> {}
```

```
# Failed validation of the "sequence" type
42 | bind("sequence")
!! wrongType {"value": 42, "expectedType": "sequence"}
```

```
# Successful validation of a type with a predicate
1 | bind(is("number", where: (n) => isLessThan(n, 10)))
>> {}
```

```
# Failed validation of a type with a predicate - wrong type
"foo" | bind(is("number", where: (n) => isLessThan(n, 10)))
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Failed validation of a type with a predicate - false predicate
42 | bind(is("number", where: (n) => isLessThan(n, 10)))
!! badValue {"value": 42}
```

```
# Successful validation of specific values
"green" | bind(oneOf("red", "green", "blue"))
>> {}
```

```
# Failed validation of specific values
"foo" | bind(oneOf("red", "green", "blue"))
!! badValue {"value": "foo", "options": ["red", "green", "blue"]}
```

```
# Successful validation of a union
"foo" | bind(either("string", "number"))
>> {}
```

```
# Failed validation of a union
null | bind(either("string", "number"))
!! wrongType {"value": null}
```

```
# Successful validation of a uniform array
["foo", "bar"] | bind(arrayOf("string"))
>> {}
```

```
# Failed validation of a uniform array - not an array
"foo" | bind(arrayOf("string"))
!! wrongType {"value": "foo", "expectedType": "array"}
```

```
# Failed validation of a uniform array - wrong element type
["foo", 1] | bind(arrayOf("string"))
!! badElement {"value": ["foo", 1], "index": 2}
```

```
# Successful validation of an array schema
["foo", 42] | bind(["string", "number"])
>> {}
```

```
# Failed validation of an array schema - not an array
42 | bind(["string", "number"])
!! wrongType {"value": 42, "expectedType": "array"}
```

```
# Failed validation of an array schema - too short
["foo"] | bind(["string", "number"])
!! missingElement {"value": ["foo"], "schema": ["string", "number"]}
```

```
# Failed validation of an array schema - wrong element type
["foo", "bar"] | bind(["string", "number"])
!! badElement {"value": ["foo", "bar"], "index": 2}
```

```
# Successful validation of a uniform object - values only
{x: 42} | bind(objectOf(values: "number"))
>> {}
```

```
# Successful validation of a uniform object - keys and values
{x: 42} | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
>> {}
```

```
# Failed validation of a uniform object - not an object
"foo" | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Failed validation of a uniform object - bad key
{za: 42} | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
!! badKey {"key": "za"}
```

```
# Failed validation of a uniform object - bad property value
{z: "foo"} | bind(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
!! badProperty {"key": "z", "value": "foo"}
```

## Automatic Binding

```
# Automatic binding of object properties
{name: "John", age: 42, job: "doctor"} | bind({name: "string", age: "number"})
>> {name: "John", age: 42}
```

```
# Failed object binding - not an object
"foo" | bind({name: "string", age: "number"})
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Failed object binding - missing key
{name: "John"} | bind({name: "string", age: "number"})
!! missingProperty {"value": {"name": "John"}, "key": "age"}
```

```
# Failed object binding - wrong property type
{name: "John", age: "middle"} | bind({name: "string", age: "number"})
!! badProperty {"value": {"name": "John", "age": "middle"}, "key": "age"}
```

```
# Lifting bindings out of an array
[{name: "John", age: 42}, {name: "Kate", age: 33}] | bind(arrayOf({name: "string", age: "number"}))
>> {name: ["John", "Kate"], age: [42, 33]}
```

## Explicit Binding

```
# Explicit binding of a single value
42 | bind("number" | as("answer"))
>> {answer: 42}
```

```
# Failed explicit binding of a single value
"foo" | bind("number" | as("answer"))
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Explicit and implicit binding
{name: "John", age: 42} | bind({name: "string", age: "number"} | as("person"))
>> {person: {name: "John", age: 42}, name: "John", age: 42}
```

```
# Explicit binding of array elements
["foo", 42] | bind(["string" | as("question"), "number" | as("answer")])
>> {question: "foo", answer: 42}
```

```
# Failed binding of array elements - missing element
["foo"] | bind(["string" | as("question"), "number" | as("answer")])
!! missingElement {"value": ["foo"]}
```

```
# Explicit binding of uniform array elements
["foo", "bar"] | bind(arrayOf("string" | as("word")))
>> {word: ["foo", "bar"]}
```

```
# Explicit binding of uniform array elements on an empty array
[] | bind(arrayOf("string" | as("word")))
>> {word: []}
```

## Default Values

```
# Default value for missing array element
["foo"] | bind(["string" | as("question"), "number" | as("answer") | default(42)])
>> {question: "foo", answer: 42}
```

```
# Default value for present array element
["foo", 97] | bind(["string" | as("question"), "number" | as("answer") | default(42)])
>> {question: "foo", answer: 97}
```

```
# Default value for missing object property
{name: "John"} | bind({name: "string", age: "number" | default(null)})
>> {name: "John", age: null}
```

```
# Default value for present object property
{name: "John", age: 42} | bind({name: "string", age: "number" | default(null)})
>> {name: "John", age: 42}
```

## Rest Bindings

```
# Rest binding for array elements
["foo", 42, 97] | bind(["string" | as("question"), rest("number") | as("answers")])
>> {question: "foo", answers: [42, 97]}
```

```
# Empty rest binding for array elements
["foo"] | bind(["string" | as("question"), rest("number") | as("answers")])
>> {question: "foo", answers: []}
```

```
# Rest binding for object elements
{foo: "bar", x: 1, y: 2} | bind({foo: "string", vars: rest("number")})
>> {foo: "bar", vars: {x: 1, y: 2}}
```

## Errors

```
# Error short-circuiting in type checking
plus("foo") | bind("string")
!! wrongArgumentType {"value": "foo"}
```

```
# Error short-circuiting in array schema
[plus("foo")] | bind(["any" | as("answer")])
!! wrongArgumentType {"value": "foo"}
```

```
# Error short-circuiting in array rest binding
[plus("foo")] | bind([rest("any") | as("answers")])
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
