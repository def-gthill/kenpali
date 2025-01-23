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
            | butIf(isEmpty, $ toString(i))
        )
    )
    | toArray
);
fizzbuzz(16)
>> ["1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz", "16"]
```

```
# Collatz
collatzStep = (n) => if(
    n | isDivisibleBy(2),
    then: $ n | dividedBy(2),
    else: $ n | times(3) | plus(1),
);
collatz = (start) => (
    start
    | build(collatzStep)
    | continueIf(| isMoreThan(1))
    | toArray
);
collatz(7)
>> [7, 22, 11, 34, 17, 52, 26, 13, 40, 20, 10, 5, 16, 8, 4, 2, 1]
```

```
# Primes
{numbers: 2 | to(100) | toArray, index: 1}
| build(({numbers:, index:}) => {
    numbers: (
        numbers
        | where((n) => (
            n | equals(numbers @ index) | or(
                $ n | isDivisibleBy(numbers @ index) | not
            )
        ))
        | toArray
    ),
    index: index | up,
})
| while(({numbers:, index:}) => (
    index | isAtMost(numbers | length)
))
| last
|.numbers
>> [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97]
```

```
# Mergesort
merge = (list1, list2) => (
    head1 = list1 | first;
    head2 = list2 | first;
    take1 = $ newStream(
        value: $ head1,
        next: $ merge(list1 | dropFirst, list2),
    );
    take2 = $ newStream(
        value: $ head2,
        next: $ merge(list1, list2 | dropFirst),
    );
    ifs(
        [$ head1 | isNull, take2],
        [$ head2 | isNull, take1],
        [$ head2 | isLessThan(head1), take2],
        else: take1,
    )
);
mergesort = (sequence) => (
    array = sequence | toArray;
    if(
        array | length | isAtMost(1),
        then: $ array,
        else: $ (
            halfway = array | length | quotientBy(2);
            firstHalfSorted = array
            | keepFirst(halfway)
            | mergesort
            | thenRepeat(null);
            secondHalfSorted = array
            | dropFirst(halfway)
            | mergesort
            | thenRepeat(null);
            merge(firstHalfSorted, secondHalfSorted)
            | while(| isNull | not)
            | toArray
        )
    )
);
[
    [] | mergesort,
    1 | to(10) | mergesort,
    10 | to(1, by: -1) | mergesort,
    ["foo", "bar", "baz", "spam", "eggs"] | mergesort,
]
>> [
    [],
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    ["bar", "baz", "eggs", "foo", "spam"],
]
```
