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
    join(["Hello, ", name, "!"])
);
hello("world")
>> "Hello, world!"
```

```
# Fizzbuzz
fizzbuzz = (n) => (
    1 | to(n) | transform(
        (i) => (
            ""
            | butIf(i | isDivisibleBy(3), (s) => join([s, "Fizz"]))
            | butIf(i | isDivisibleBy(5), (s) => join([s, "Buzz"]))
            | butIf(isEmpty, () => toString(i))
        )
    )
);
fizzbuzz(16)
>> ["1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz", "16"]
```

```
# Collatz
collatzStep = (n) => if(
    n | isDivisibleBy(2),
    then: () => n | dividedBy(2),
    else: () => n | times(3) | plus(1),
);
collatz = (n) => (
    n | build(
        (previous) => {
            next: collatzStep(previous),
            out: [previous],
            continueIf: previous | isMoreThan(1),
        }
    )
);
collatz(7)
>> [7, 22, 11, 34, 17, 52, 26, 13, 40, 20, 10, 5, 16, 8, 4, 2, 1]
```

```
# Primes
[2 | to(100), 1] | repeat((args) => (
  [numbers, i] = args;
  next = numbers
    | where((n) => or(
       n | equals(numbers @ i),
       () => not(n | isDivisibleBy(numbers @ i))
    ));
  {
    while: not(next | equals(numbers)),
    next: [next, increment(i)],
  }
)) @ 1
>> [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97]
```
