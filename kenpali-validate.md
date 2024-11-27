## Type Schemas

```
# Successful validation of a simple type
42 | validate("number")
>> true
```

```
# Failed validation of a simple type
"foo" | validate("number")
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Successful validation of the "any" type
"foo" | validate("any")
>> true
```

```
# Successful validation of the "object" type
{foo: 1, bar: 2} | validate("object")
>> true
```

```
# Failed validation of the "object" type
"foo" | validate("object")
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Successful validation of the "function" type
typeOf | validate("function")
>> true
```

```
# Failed validation of the "function" type
"foo" | validate("function")
!! wrongType {"value": "foo", "expectedType": "function"}
```

```
# Successful validation of the "sequence" type
[1, 2, 3] | validate("sequence")
>> true
```

```
# Failed validation of the "sequence" type
42 | validate("sequence")
!! wrongType {"value": 42, "expectedType": "sequence"}
```

## Type with Predicate

```
# Successful validation of a type with a predicate
1 | validate(is("number", where: (n) => isLessThan(n, 10)))
>> true
```

```
# Failed validation of a type with a predicate - wrong type
"foo" | validate(is("number", where: (n) => isLessThan(n, 10)))
!! wrongType {"value": "foo", "expectedType": "number"}
```

```
# Failed validation of a type with a predicate - false predicate
42 | validate(is("number", where: (n) => isLessThan(n, 10)))
!! badValue {"value": 42}
```

## One-Of

```
# Successful validation of specific values
"green" | validate(oneOf("red", "green", "blue"))
>> true
```

```
# Failed validation of specific values
"foo" | validate(oneOf("red", "green", "blue"))
!! badValue {"value": "foo", "options": ["red", "green", "blue"]}
```

## Either

```
# Successful validation of a union
"foo" | validate(either("string", "number"))
>> true
```

```
# Failed validation of a union
null | validate(either("string", "number"))
!! wrongType {"value": null}
```

## Uniform Arrays

```
# Successful validation of a uniform array
["foo", "bar"] | validate(arrayOf("string"))
>> true
```

```
# Failed validation of a uniform array - not an array
"foo" | validate(arrayOf("string"))
!! wrongType {"value": "foo", "expectedType": "array"}
```

```
# Failed validation of a uniform array - wrong element type
["foo", 1] | validate(arrayOf("string"))
!! badElement {"value": ["foo", 1], "index": 2}
```

## Tuples

```
# Successful validation of a tuple schema
["foo", 42] | validate(tupleLike(["string", "number"]))
>> true
```

```
# Failed validation of a tuple schema - not an array
42 | validate(tupleLike(["string", "number"]))
!! wrongType {"value": 42, "expectedType": "array"}
```

```
# Failed validation of a tuple schema - too short
["foo"] | validate(tupleLike(["string", "number"]))
!! missingElement {"value": ["foo"], "schema": ["string", "number"]}
```

```
# Successful validation of a tuple schema with optional
["foo"] | validate(tupleLike(["string", optional("number")]))
>> true
```

```
# Failed validation of a tuple schema - wrong element type
["foo", "bar"] | validate(tupleLike(["string", "number"]))
!! badElement {"value": ["foo", "bar"], "index": 2}
```

## Uniform Objects

```
# Successful validation of a uniform object - values only
{x: 42} | validate(objectOf(values: "number"))
>> true
```

```
# Successful validation of a uniform object - keys and values
{x: 42} | validate(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
>> true
```

```
# Failed validation of a uniform object - not an object
"foo" | validate(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Failed validation of a uniform object - bad key
{za: 42} | validate(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
!! badKey {"key": "za"}
```

```
# Failed validation of a uniform object - bad property value
{z: "foo"} | validate(objectOf(keys: is("string", where: (s) => (length(s) | equals(1))), values: "number"))
!! badProperty {"value": {"z": "foo"}, "key": "z"}
```

## Records

```
# Successful validation of a record
{name: "John", age: 42, job: "doctor"} | validate(recordLike({name: "string", age: "number"}))
>> true
```

```
# Failed validation of a record - not an object
"foo" | validate(recordLike({name: "string", age: "number"}))
!! wrongType {"value": "foo", "expectedType": "object"}
```

```
# Failed validation of a record - missing key
{name: "John"} | validate(recordLike({name: "string", age: "number"}))
!! missingProperty {"value": {"name": "John"}, "key": "age"}
```

```
# Successful validation of a record with optional
{name: "John"} | validate(recordLike({name: "string", age: optional("number")}))
>> true
```

```
# Failed validation of a record - wrong property type
{name: "John", age: "middle"} | validate(recordLike({name: "string", age: "number"}))
!! badProperty {"value": {"name": "John", "age": "middle"}, "key": "age"}
```
