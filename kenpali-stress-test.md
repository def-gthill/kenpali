# Kenpali Stress Test

## Arrays

```
# Destructuring an array with an object pattern
{foo:, bar:} = [1, 2];
foo
!! wrongType {"value": [1, 2], "expectedType": "object"}
```

## Objects

```
# Destructuring an object with an array pattern
[foo, bar] = {foo: 42, bar: 97};
foo
!! wrongType {"value": {"foo": 42, "bar": 97}, "expectedType": "array"}
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

In this example, `x` has already gone out of scope by the time `baz` is *declared*, so `baz` can't just capture `x` at declaration time.

```
# Nasty nested closure
foo = () => (
    x = 42;
    bar = () => (
        baz = () => x;
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
