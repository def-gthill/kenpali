# Kenpali Stress Test

## Defining and Calling Functions

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
