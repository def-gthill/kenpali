# Kenpali Test Programs

```
# Simple function call
negative(42)
>> -42
```

```
TODO Pipeline
2 | plus(3) | times(4) | minus(5) | dividedBy(6)
>> 2.5
```

```
TODO Hello world
hello = (name) => (
    join("Hello, ", name, "!")
);
hello("world")
>> "Hello, world!"
```

```
TODO Fizzbuzz
fizzbuzz = (n) => (
    1 | to(n) | forEach(
        (i) => (
            ""
            | butIf(i | isDivisibleBy(3), (s) => join(s, "Fizz"))
            | butIf(i | isDivisibleBy(5), (s) => join(s, "Buzz"))
            | butIf(isEmpty, toString(i))
        )
    )
)
```
