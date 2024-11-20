# Kenpali Stress Test

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
