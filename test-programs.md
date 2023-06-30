# Kenpali Test Programs

```
# Simple function call
negative(42)
>> -42
```

```
# Pipeline
2 | plus(3) | times(4) | minus(5) | dividedBy(6)
>> 2.5
```

```
# Hello world
hello = (name) => (
    join("Hello, ", name, "!")
);
hello("world")
>> "Hello, world!"
```

```
# Fizzbuzz
fizzbuzz = (n) => (
    1 | to(n) | forEach(
        (i) => (
            ""
            | butIf(i | isDivisibleBy(3), (s) => join(s, "Fizz"))
            | butIf(i | isDivisibleBy(5), (s) => join(s, "Buzz"))
            | butIf(isEmpty, toString(i))
        )
    )
);
fizzbuzz(16)
>> ["1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz", "16"]
```
