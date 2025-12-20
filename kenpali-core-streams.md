# Kenpali Core Streams Specification

Tests to confirm that the stream functions available in the core module are well-behaved:

- They don't compute any more values than necessary.
- They don't compute the same values multiple times.
- They don't cause stack overflows.

## build|build

```
# Build doesn't call the callback if no values are requested
1 | build($ 1 @ 1);
42
>> 42
```

```
# Build only invokes the function when needed
1
| build((x) => if(
    x | ge(3),
    then: $ throw(newError("tooBig")),
    else: $ x | up
))
| keepFirst(3)
| toArray
>> [1, 2, 3]
```

```
# Build only invokes the function once for each iteration
out = newMutableArray();
1 | build((x) => (out.append(x); x | up))
| keepFirst(4)
| toArray;
out.elements()
>> [1, 2, 3]
```

```
# Build doesn't overflow the stack
1 | build(up) @ 9999
>> 9999
```

## isEmpty|isEmpty

```
# Is empty doesn't advance the stream
newStream(value: $ throw(newError("badIdea")), next: emptyStream) | isEmpty
>> false
```

## first|first

```
# First doesn't advance beyond the first element
[
    1 | repeat | first,
    1 | build($ throw(newError("badIdea"))) | first,
]
>> [1, 1]
```

## transform|transform

```
# Transform doesn't advance its input beyond what it is asked for
[
    1 | build(| up)
    | transform((i) => mul(i, i))
    | keepFirst(3)
    | toArray,
    1
    | build($ throw(newError("badIdea")))
    | transform((i) => mul(i, i))
    | first,
]
>> [[1, 4, 9], 1]
```

## running|running

```
# Running only invokes `next` when needed
[2, 8, 9, 3, 7]
| running(
    start: 0,
    next: $ throw(newError("badIdea"))
)
| first
>> 0
```

```
# Running doesn't overflow the stack
repeat(1) | running(start: 1, next: (number, state:) => state | add(number)) @ 9999
>> 9999
```

## with|with

```
# With only advances the input stream when needed
"foo" | build($ throw(newError("badIdea"))) | with(length) | first
>> [3, "foo"]
```

## withIndex|withIndex

```
# With index only advances the input stream when needed
1 | build($ throw(newError("badIdea"))) | withIndex | first
>> [1, 1]
```

## keepFirst|keepFirst

```
# Keep first doesn't advance past what it keeps
[
    1 | build(| mul(2)) | keepFirst(3) | toArray,
    1 | build($ throw(newError("badIdea"))) | keepFirst(1) | toArray,
]
>> [[1, 2, 4], [1]]
```

```
# Keep first doesn't advance past what it's asked for
1 | build($ throw(newError("badIdea"))) | keepFirst(3) | keepFirst(1) | toArray
>> [1]
```

## dropFirst|dropFirst

```
# Drop first doesn't ask for dropped values
badStart = (
    streamFrom = (i) => (
        newStream(
            value: $ if(
                i | le(3),
                then: $ throw(newError("badIdea")),
                else: $ i
            ),
            next: $ streamFrom(i | up)
        )
    );
    streamFrom(1)
);
badStart | dropFirst(3) | keepFirst(3) | toArray
>> [4, 5, 6]
```

```
# Drop first doesn't advance past what it's asked for
[[42, 97, 6], 1 | build($ throw(newError("badIdea")))]
| flatten
| dropFirst(1)
| keepFirst(3)
| toArray
>> [97, 6, 1]
```

## slice|slice

```
# Slice doesn't advance beyond what it's asked for
[
    1 | build(| mul(2)) | slice(from: 2, to: 4) | toArray,
    [[42, 97, 6], 1 | build($ throw(newError("badIdea")))]
    | flatten
    | slice(from: 2, to: 4)
    | toArray,
]
>> [[2, 4, 8], [97, 6, 1]]
```

## while|while

```
# While doesn't ask for values beyond the stopping condition
1 | build($ throw(newError("badIdea"))) | while(| lt(0)) | toArray
>> []
```

## continueIf|continueIf

```
# Continue-If doesn't ask for values beyond the stopping condition
1 | build($ throw(newError("badIdea"))) | continueIf(| lt(0)) | toArray
>> [1]
```

## dropWhile|dropWhile
```
# Drop while doesn't advance beyond what it's asked for
[[1, 2, 1, 1, 3, 4, 1], 1 | build($ throw(newError("badIdea")))]
| flatten
| dropWhile(| lt(3))
| keepFirst(4)
| toArray
>> [3, 4, 1, 1]
```

## sliding|sliding

```
# Sliding doesn't ask for values beyond its last window
[[2, 8, 9, 3], 1 | build($ throw(newError("badIdea")))]
| flatten
| sliding(3)
| keepFirst(3)
| toArray
>> [[2, 8, 9], [8, 9, 3], [9, 3, 1]]
```

## where|where

```
# Where doesn't ask for values beyond the last element it needs
[[1, 10, 2, 9, 3, 12], 1 | build($ throw(newError("badIdea")))]
| flatten
| where(| lt(10))
| keepFirst(5)
| toArray
>> [1, 2, 9, 3, 1]
```

## distinct|distinct

```
# Distinct doesn't ask for values beyond the last element it needs
[[42, 97, 73], 42 | build($ throw(newError("badIdea")))]
| flatten
| distinct
| keepFirst(3)
| toArray
>> [42, 97, 73]
```

## zip|zip

```
# Zip doesn't ask for values beyond the shortest sequence
1 | build($ throw(newError("badIdea"))) | zip(["foo"]) | toArray
>> [[1, "foo"]]
```

```
# Zip doesn't advance beyond what it's asked for
1 | build($ throw(newError("badIdea"))) | zip(["foo", "bar"]) | keepFirst(1) | toArray
>> [[1, "foo"]]
```

## unzip|unzip

```
# Unzip doesn't advance beyond what any of its output streams asks for
[1, "one"] | build($ throw(newError("badIdea")))
| unzip
| transform(first)
| toArray
>> [1, "one"]
```

## flatten|flatten

```
# Flatten doesn't advance the current sub-stream beyond what it's asked for
[[1], 1 | build($ throw(newError("badIdea")))]
| flatten
| keepFirst(2)
| toArray
>> [1, 1]
```

```
# Flatten doesn't ask for any values from the next sub-stream if the current sub-stream provides all the values it needs
[[1], newStream(value: $ throw(newError("badIdea")), next: emptyStream)]
| flatten
| keepFirst(1)
| toArray
>> [1]
```

```
# Flatten doesn't advance the outer stream beyond what it's asked for
[1] | build($ throw(newError("badIdea"))) | flatten | first
>> 1
```

## dissect|dissect

```
# Dissect doesn't advance beyond what it's asked for
9 | build($ throw(newError("badIdea"))) | dissect(| ge(8)) | first
>> [9]
```

## chunk|chunk

```
# Chunk doesn't advance beyond what it's asked for
[1 | to(9), newStream(value: $ throw(newError("badIdea")), next: emptyStream)]
| flatten
| chunk(3)
| keepFirst(3)
| toArray
>> [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

## at|at

```
# At doesn't advance beyond what it's asked for
1 | build($ throw(newError("badIdea"))) | at(1)
>> 1
```
