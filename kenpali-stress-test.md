# Kenpali Stress Test

## Arrays

```
# Destructuring an array with an object pattern
{foo:, bar:} = [1, 2];
foo
!! wrongType {"value": [1, 2], "expectedType": "Object"}
```

```
# Nested array destructure with a default
[foo, [bar, baz] = [42, 73]] = [216];
[bar, foo, baz]
>> [42, 216, 73]
```

```
# Nested destructure of array rest
[foo, *[bar, baz], quux] = [42, 57, 73, 97, 216];
[bar, foo, quux, baz]
>> [57, 42, 216, 73]
```

## Streams

```
# Merely invoking build doesn't call the callback
1 | build($ 1 @ 1);
42
>> 42
```

```
# Indexing with a positive index
[
    2 | to(5) @ 2,
    2 | build(| times(2)) @ 2,
]
>> [3, 4]
```

```
# Indexing with a negative index
1 | to(5) @ -2
>> 4
```

```
# Destructuring a stream
[foo, bar, baz] = 1 | build(| times(2));
[bar, baz, foo]
>> [2, 4, 1]
```

```
# Destructuring a stream with rest
[foo, *rest] = 1 | build(| times(2));
[foo, rest | keepFirst(3) | toArray]
>> [1, [2, 4, 8]]
```

```
# Destructuring a stream with a middle rest
[foo, *rest, bar] = 1 | build(| times(2)) | while(| isLessThan(100));
[foo, rest, bar]
>> [1, [2, 4, 8, 16, 32], 64]
```

```
# Spreading a stream
["foo", *(1 | to(3)), "bar"]
>> ["foo", 1, 2, 3, "bar"]
```

```
# Stream values are locked in by the first traversal
answer = variable(42);
stream = 1 | to(3) | transform(| plus(answer.get()));
before = stream | toArray;
answer.set(73);
after = stream | toArray;
[before, after]
>> [[43, 44, 45], [43, 44, 45]]
```

## Objects

```
# Destructuring an object with an array pattern
[foo, bar] = {foo: 42, bar: 97};
foo
!! wrongType {"value": {"foo": 42, "bar": 97}, "expectedType": "either(Array, Stream)"}
```

```
# Nested object destructure with a default
{foo:, bar: [baz, quux] = [42, 73]} = {foo: 216};
[foo, quux, baz]
>> [216, 73, 42]
```

```
# Object destructure with a dynamic key
key = "foo";
{(key): bar} = {foo: 42};
bar
>> 42
```

```
# Immediate property access on an object
{foo: 42, bar: 97}.foo
>> 42
```

## Defining and Calling Functions

```
# Multiple optional positional parameters
foo = (a, b = 1, c = 2) => [a, b, c];
[
    foo(42),
    foo(42, 97),
    foo(42, 97, 216),
    foo(42, 97, 216, 729),
]
>> [
    [42, 1, 2],
    [42, 97, 2],
    [42, 97, 216],
    [42, 97, 216],
]
```

```
# Multiple optional positional parameters with a rest parameter in the middle
foo = (a, b = 1, *c, d = 2) => [a, b, c, d];
[
    foo(42),
    foo(42, 97),
    foo(42, 97, 216),
    foo(42, 97, 216, 729),
    foo(42, 97, 216, 729, 4321),
]
>> [
    [42, 1, [], 2],
    [42, 97, [], 2],
    [42, 97, [], 216],
    [42, 97, [216], 729],
    [42, 97, [216, 729], 4321],
]
```

```
# Destructuring parameter with a default value
foo = ({bar:, baz:} = {bar: 42, baz: 97}) => [bar, baz];
foo()
>> [42, 97]
```

```
# Rest in object destructure in parameter
foo = ({**rest, bar:}) => (
  rest
);
foo({bar: 42, baz: 97})
>> {baz: 97}
```

In this example, `x` has already gone out of scope by the time `baz` is *declared*, so `baz` can't just capture `x` at declaration time.

```
# Nasty nested closure
foo = $ (
    x = 42;
    bar = $ (
        baz = $ x;
        baz
    );
    bar
);
foo()()()
>> 42
```

## Indexing

```
# Indexing strings with escapes
string = "\"\\\/\b\f\n\r\t\u1234";
[
    string @ 1,
    string @ 5,
    string @ 9,
]
>> ["\"", "\f", "\u1234"]
```

## Forward Pipe

```
# Correct argument injection with multiple calls
foo = (a, b = 4) => (c, d = 5) => [a, b, c, d];
1 | foo(2)(3)
>> [1, 2, 3, 5]
```

```
# Blocking argument injection with multiple calls
foo = (a, b = 4) => (c, d = 5) => [a, b, c, d];
1 | (foo(2))(3)
>> [2, 4, 1, 3]
```

## Sets and Maps

```
# Set-has as callback
set = [1, 2, 3, 4, 42] | newSet;
[3, 8, 4, 42, 57] | where(set.has) | toArray
>> [3, 4, 42]
```

## Errors

```
# Error thrown after catching
1 @ 1 ! @ 1
!! wrongType {"expectedType": "String"}
```

```
# Stack traces
bar = $ (
  ($ 1 @ 1)()
);
foo = $ bar();
main = $ (
  baz = $ foo();
  baz() ! | toObject |.calls
);
main()
>> [
    {function: "$main/bar/$anon1"},
    {function: "$main/bar"},
    {function: "$main/foo"},
    {function: "$main/main/baz"},
]
```
