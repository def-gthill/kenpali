## Type Schemas

```
# Successful validation of a simple type
42 | validate(Number)
>> true
```

```
# Failed validation of a simple type
"foo" | validate(Number)
!! wrongType {"value": "foo", "expectedType": "Number"}
```

```
# Successful validation of the "any" type
"foo" | validate(Any)
>> true
```

```
# Successful validation of the "object" type
{foo: 1, bar: 2} | validate(Object)
>> true
```

```
# Failed validation of the "object" type
"foo" | validate(Object)
!! wrongType {"value": "foo", "expectedType": "Object"}
```

```
# Successful validation of the "function" type
classOf | validate(Function)
>> true
```

```
# Failed validation of the "function" type
"foo" | validate(Function)
!! wrongType {"value": "foo", "expectedType": "Function"}
```

```
# Successful validation of the "sequence" type
[1, 2, 3] | validate(Sequence)
>> true
```

```
# Failed validation of the "sequence" type
42 | validate(Sequence)
!! wrongType {"value": 42, "expectedType": "Sequence"}
```

## Type with Predicate

```
# Successful validation of a type with a predicate
1 | validate(is(Number, where: (n) => isLessThan(n, 10)))
>> true
```

```
# Failed validation of a type with a predicate - wrong type
"foo" | validate(is(Number, where: (n) => isLessThan(n, 10)))
!! wrongType {"value": "foo", "expectedType": "Number"}
```

```
# Failed validation of a type with a predicate - false predicate
42 | validate(is(Number, where: (n) => isLessThan(n, 10)))
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
"foo" | validate(either(String, Number))
>> true
```

```
# Failed validation of a union
null | validate(either(String, Number))
!! wrongType {"value": null}
```

## Uniform Arrays

```
# Successful validation of a uniform array
["foo", "bar"] | validate(arrayOf(String))
>> true
```

```
# Failed validation of a uniform array - not an array
"foo" | validate(arrayOf(String))
!! wrongType {"value": "foo", "expectedType": "Array"}
```

```
# Failed validation of a uniform array - wrong element type
["foo", 1] | validate(arrayOf(String))
!! badElement {"value": ["foo", 1], "index": 2}
```

## Tuples

```
# Successful validation of a tuple schema
["foo", 42] | validate(tupleLike([String, Number]))
>> true
```

```
# Failed validation of a tuple schema - not an array
42 | validate(tupleLike([String, Number]))
!! wrongType {"value": 42, "expectedType": "Array"}
```

```
# Failed validation of a tuple schema - too short
["foo"] | validate(tupleLike([String, Number]))
!! missingElement {"value": ["foo"], "schema": "tupleLike([String, Number])"}
```

```
# Successful validation of a tuple schema with optional
["foo"] | validate(tupleLike([String, optional(Number)]))
>> true
```

```
# Failed validation of a tuple schema - wrong element type
["foo", "bar"] | validate(tupleLike([String, Number]))
!! badElement {"value": ["foo", "bar"], "index": 2}
```

## Uniform Objects

```
# Successful validation of a uniform object - values only
{x: 42} | validate(objectOf(values: Number))
>> true
```

```
# Successful validation of a uniform object - keys and values
{x: 42} | validate(objectOf(keys: is(String, where: (s) => (length(s) | equals(1))), values: Number))
>> true
```

```
# Failed validation of a uniform object - not an object
"foo" | validate(objectOf(keys: is(String, where: (s) => (length(s) | equals(1))), values: Number))
!! wrongType {"value": "foo", "expectedType": "Object"}
```

```
# Failed validation of a uniform object - bad key
{za: 42} | validate(objectOf(keys: is(String, where: (s) => (length(s) | equals(1))), values: Number))
!! badKey {"key": "za"}
```

```
# Failed validation of a uniform object - bad property value
{z: "foo"} | validate(objectOf(keys: is(String, where: (s) => (length(s) | equals(1))), values: Number))
!! badProperty {"value": {"z": "foo"}, "key": "z"}
```

## Records

```
# Successful validation of a record
{name: "John", age: 42, job: "doctor"} | validate(recordLike({name: String, age: Number}))
>> true
```

```
# Failed validation of a record - not an object
"foo" | validate(recordLike({name: String, age: Number}))
!! wrongType {"value": "foo", "expectedType": "Object"}
```

```
# Failed validation of a record - missing key
{name: "John"} | validate(recordLike({name: String, age: Number}))
!! missingProperty {"value": {"name": "John"}, "key": "age"}
```

```
# Successful validation of a record with optional
{name: "John"} | validate(recordLike({name: String, age: optional(Number)}))
>> true
```

```
# Failed validation of a record - wrong property type
{name: "John", age: "middle"} | validate(recordLike({name: String, age: Number}))
!! badProperty {"value": {"name": "John", "age": "middle"}, "key": "age"}
```
