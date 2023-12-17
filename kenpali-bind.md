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
!! badValue {"value": null}
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
# Successful validation of a uniform object
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

## Binding

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
