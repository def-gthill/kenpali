# Kenpali Core Types Specification

Tests to confirm that the types available in the core module behave as expected.

## Type Predicates|type-predicates

The core module has various `isX` functions to check for specific types. These are equivalent to `| matches(X)`, but `isX` is often faster and more readable.

```
# Is null
[
    isNull(null),
    isNull(false),
    isNull(true),
    isNull(42),
    isNull("foo"),
    isNull([1, 2, 3]),
    isNull(1 | to(3)),
    isNull({foo: 1, bar: 2}),
    isNull(classOf),
    isNull((x) => x),
    isNull(newError("badIdea")),
    isNull(Number),
    isNull(Sequence),
]
>> [true, false, false, false, false, false, false, false, false, false, false, false, false]
```

```
# Is boolean
[
    isBoolean(null),
    isBoolean(false),
    isBoolean(true),
    isBoolean(42),
    isBoolean("foo"),
    isBoolean([1, 2, 3]),
    isBoolean(1 | to(3)),
    isBoolean({foo: 1, bar: 2}),
    isBoolean(classOf),
    isBoolean((x) => x),
    isBoolean(newError("badIdea")),
    isBoolean(Number),
    isBoolean(Sequence),
]
>> [false, true, true, false, false, false, false, false, false, false, false, false, false]
```

```
# Is number
[
    isNumber(null),
    isNumber(false),
    isNumber(true),
    isNumber(42),
    isNumber("foo"),
    isNumber([1, 2, 3]),
    isNumber(1 | to(3)),
    isNumber({foo: 1, bar: 2}),
    isNumber(classOf),
    isNumber((x) => x),
    isNumber(newError("badIdea")),
    isNumber(Number),
    isNumber(Sequence),
]
>> [false, false, false, true, false, false, false, false, false, false, false, false, false]
```

```
# Is string
[
    isString(null),
    isString(false),
    isString(true),
    isString(42),
    isString("foo"),
    isString([1, 2, 3]),
    isString(1 | to(3)),
    isString({foo: 1, bar: 2}),
    isString(classOf),
    isString((x) => x),
    isString(newError("badIdea")),
    isString(Number),
    isString(Sequence),
]
>> [false, false, false, false, true, false, false, false, false, false, false, false, false]
```

```
# Is array
[
    isArray(null),
    isArray(false),
    isArray(true),
    isArray(42),
    isArray("foo"),
    isArray([1, 2, 3]),
    isArray(1 | to(3)),
    isArray({foo: 1, bar: 2}),
    isArray(classOf),
    isArray((x) => x),
    isArray(newError("badIdea")),
    isArray(Number),
    isArray(Sequence),
]
>> [false, false, false, false, false, true, false, false, false, false, false, false, false]
```

```
# Is stream
[
    isStream(null),
    isStream(false),
    isStream(true),
    isStream(42),
    isStream("foo"),
    isStream([1, 2, 3]),
    isStream(1 | to(3)),
    isStream({foo: 1, bar: 2}),
    isStream(classOf),
    isStream((x) => x),
    isStream(newError("badIdea")),
    isStream(Number),
    isStream(Sequence),
]
>> [false, false, false, false, false, false, true, false, false, false, false, false, false]
```

```
# Is object
[
    isObject(null),
    isObject(false),
    isObject(true),
    isObject(42),
    isObject("foo"),
    isObject([1, 2, 3]),
    isObject(1 | to(3)),
    isObject({foo: 1, bar: 2}),
    isObject(classOf),
    isObject((x) => x),
    isObject(newError("badIdea")),
    isObject(Number),
    isObject(Sequence),
]
>> [false, false, false, false, false, false, false, true, false, false, false, false, false]
```

```
# Is function
[
    isFunction(null),
    isFunction(false),
    isFunction(true),
    isFunction(42),
    isFunction("foo"),
    isFunction([1, 2, 3]),
    isFunction(1 | to(3)),
    isFunction({foo: 1, bar: 2}),
    isFunction(classOf),
    isFunction((x) => x),
    isFunction(newError("badIdea")),
    isFunction(Number),
    isFunction(Sequence),
]
>> [false, false, false, false, false, false, false, false, true, true, false, false, false]
```

```
# Is error
[
    isError(null),
    isError(false),
    isError(true),
    isError(42),
    isError("foo"),
    isError([1, 2, 3]),
    isError(1 | to(3)),
    isError({foo: 1, bar: 2}),
    isError(classOf),
    isError((x) => x),
    isError(newError("badIdea")),
    isError(Number),
    isError(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, true, false, false]
```

```
# Is class
[
    isClass(null),
    isClass(false),
    isClass(true),
    isClass(42),
    isClass("foo"),
    isClass([1, 2, 3]),
    isClass(1 | to(3)),
    isClass({foo: 1, bar: 2}),
    isClass(classOf),
    isClass((x) => x),
    isClass(newError("badIdea")),
    isClass(Number),
    isClass(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, true, false]
```

```
# Is protocol
[
    isProtocol(null),
    isProtocol(false),
    isProtocol(true),
    isProtocol(42),
    isProtocol("foo"),
    isProtocol([1, 2, 3]),
    isProtocol(1 | to(3)),
    isProtocol({foo: 1, bar: 2}),
    isProtocol(classOf),
    isProtocol((x) => x),
    isProtocol(newError("badIdea")),
    isProtocol(Number),
    isProtocol(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, false, true]
```

```
# Is sequence
[
    isSequence(null),
    isSequence(false),
    isSequence(true),
    isSequence(42),
    isSequence("foo"),
    isSequence([1, 2, 3]),
    isSequence(1 | to(3)),
    isSequence({foo: 1, bar: 2}),
    isSequence(classOf),
    isSequence((x) => x),
    isSequence(newError("badIdea")),
    isSequence(Number),
    isSequence(Sequence),
]
>> [false, false, false, false, true, true, true, false, false, false, false, false, false]
```

```
# Is type
[
    isType(null),
    isType(false),
    isType(true),
    isType(42),
    isType("foo"),
    isType([1, 2, 3]),
    isType(1 | to(3)),
    isType({foo: 1, bar: 2}),
    isType(classOf),
    isType((x) => x),
    isType(newError("badIdea")),
    isType(Number),
    isType(Sequence),
]
>> [false, false, false, false, false, false, false, false, false, false, false, true, true]
```

```
# Is instance
[
    isInstance(null),
    isInstance(false),
    isInstance(true),
    isInstance(42),
    isInstance("foo"),
    isInstance([1, 2, 3]),
    isInstance(1 | to(3)),
    isInstance({foo: 1, bar: 2}),
    isInstance(classOf),
    isInstance((x) => x),
    isInstance(newError("badIdea")),
    isInstance(Number),
    isInstance(Sequence),
]
>> [false, false, false, false, false, false, true, false, false, false, true, true, true]
```

## Functions that Accept Collections|collection-arguments

### sum|sum

```
# Sum on collections
[
    sum(1 | to(5)),
    sum([1, 2, 3, 4, 5] | newSet)
]
>> [15, 15]
```

### least|least

```
# Least on collections
[
    [97, 42, 216] | toStream | least,
    [97, 42, 216] | newSet | least,
]
>> [42, 42]
```

### greatest|greatest

```
# Greatest on collections
[
    [97, 42, 216] | toStream | greatest,
    [97, 42, 216] | newSet | greatest,
]
>> [216, 216]
```

### count|count

```
# Count on collections
[
    [1, 10, 2, 9, 3, 12] | toStream | count(| lt(10)),
    [1, 10, 2, 9, 3, 12] | newSet | count(| lt(10)),
]
>> [4, 4]
```

### forAll|forAll

```
# For all on collections
[
    [1, 2, 3] | toStream | forAll(| lt(10)),
    [1, 2, 3] | newSet | forAll(| lt(10)),
]
>> [true, true]
```

### forSome|forSome

```
# For some on collections
[
    [41, 42, 43] | toStream | forSome(| lt(10)),
    [41, 42, 43] | newSet | forSome(| lt(10)),
]
>> [false, false]
```

### sort|sort

```
# Sort on collections
[
    ["foo", "bar", "spam", "eggs"] | toStream | sort,
    ["foo", "bar", "spam", "eggs"] | newSet | sort,
]
>> [["bar", "eggs", "foo", "spam"], ["bar", "eggs", "foo", "spam"]]
```

### group|group

```
# Group on collections
[
    [["foo", 42], ["bar", 97], ["foo", 216], ["foo", 729], ["spam", 57]] | toStream | group,
    [["foo", 42], ["bar", 97], ["foo", 216], ["foo", 729], ["spam", 57]] | newSet | group | sort,
]
>> [
    [
        ["foo", [42, 216, 729]],
        ["bar", [97]],
        ["spam", [57]],
    ],
    [
        ["bar", [97]],
        ["foo", [42, 216, 729]],
        ["spam", [57]],
    ]
]
```

### groupBy|groupBy

```
# Group by on collections
[
    ["foo", "bar", "spam", "eggs"] | toStream | groupBy(length),
    ["foo", "bar", "spam", "eggs"] | newSet | groupBy(length) | sort,
]
>> [
    [
        [3, ["foo", "bar"]],
        [4, ["spam", "eggs"]],
    ],
    [
        [3, ["foo", "bar"]],
        [4, ["spam", "eggs"]],
    ]
]
```

### forEach|forEach

```
# For each on non-sequence
result = newMutableArray();
["foo", "bar", "baz"] | newSet | forEach(result.append);
result.elements() | sort
>> ["bar", "baz", "foo"]
```

### isEmpty|isEmpty

```
# Is empty on collections
[
    emptyStream() | isEmpty,
    [1] | toStream | isEmpty,
    newSet() | isEmpty,
    [1] | newSet | isEmpty,
]
>> [true, false, true, false]
```

### transform|transform
```
# Transform on collections
[
    1 | to(3) | transform((i) => i | mul(i)) | toArray,
    1 | to(3) | newSet | transform((i) => mul(i, i)) | sort,
]
>> [[1, 4, 9], [1, 4, 9]]
```

### where|where

```
# Where on collections
[
    "foobar" | where(| eq("b") | not) | join,
    [1, 10, 2, 9, 3, 12] | where(| lt(10)) | toArray,
    [1, 10, 2, 9, 3, 12] | newSet | where(| lt(10)) | sort,
]
>> ["fooar", [1, 2, 9, 3], [1, 2, 3, 9]]
```

### distinct|distinct
```
# Distinct on collections
[
    [42, 97, 42, 73] | toStream | distinct | toArray,
    [42, 97, 42, 73] | newSet | distinct | sort,
]
>> [[42, 97, 73], [42, 73, 97]]
```

### newSet|newSet

```
# Set creation
[
    "foobar" | newSet | sort,
    1 | build(| mul(2)) | keepFirst(3) | newSet | sort,
    [42, 97, 42, 73] | newSet | newSet | sort,
]
>> [
    ["a", "b", "f", "o", "r"],
    [1, 2, 4],
    [42, 73, 97],
]
```

### newMap|newMap


```
# Map creation
[
    [["foo", 42], ["bar", 73], ["bar", 97]] | toStream | newMap | sort,
    [["foo", 42], ["bar", 97]] | newSet | newMap | sort,
]
>> [
    [["bar", 97], ["foo", 42]],
    [["bar", 97], ["foo", 42]],
]
```

### newMutableSet|newMutableSet

```
# Mutable set creation
[
    "foobar" | newMutableSet | sort,
    1 | build(| mul(2)) | keepFirst(3) | newMutableSet | sort,
    [42, 97, 42, 73] | newSet | newMutableSet | sort,
]
>> [
    ["a", "b", "f", "o", "r"],
    [1, 2, 4],
    [42, 73, 97],
]
```

### newMutableMap|newMutableMap

```
# Mutable map creation
[
    [["foo", 42], ["bar", 73], ["bar", 97]] | toStream | newMutableMap | sort,
    [["foo", 42], ["bar", 97]] | newSet | newMutableMap | sort,
]
>> [
    [["bar", 97], ["foo", 42]],
    [["bar", 97], ["foo", 42]],
]
```

## Functions that Accept Sequences|sequence-arguments

### join|join

```
# Join on sequences
[
    "foobar" | join(on: "|"),
    ["foo", "bar", "baz"] | toStream | join(on: "|"),
    ["foo", "bar", "baz"] | newMutableArray | join(on: "|"),
]
>> ["f|o|o|b|a|r", "foo|bar|baz", "foo|bar|baz"]
```

### joinLines|joinLines

```
# Join lines on sequences
[
    "foobar" | joinLines,
    ["foo", "bar", "baz"] | toStream | joinLines,
    ["foo", "bar", "baz"] | newMutableArray | joinLines,
]
>> ["f\no\no\nb\na\nr", "foo\nbar\nbaz", "foo\nbar\nbaz"]
```

### length|length

```
# Length on sequences
[
    "foobar" | length,
    ["foo", "bar", "baz"] | toStream | length,
    ["foo", "bar", "baz"] | newMutableArray | length,
]
>> [6, 3, 3]
```

### keepLast|keepLast

```
# Keep last on sequences
[
    "foobar" | keepLast(2),
    ["foo", "bar", "baz"] | toStream | keepLast(2),
    ["foo", "bar", "baz"] | newMutableArray | keepLast(2),
]
>> ["ar", ["bar", "baz"], ["bar", "baz"]]
```

### dropLast|dropLast

```
# Drop last on sequences
[
    "foobar" | dropLast(2),
    ["foo", "bar", "baz"] | toStream | dropLast(2),
    ["foo", "bar", "baz"] | newMutableArray | dropLast(2),
]
>> ["foob", ["foo"], ["foo"]]
```

### reverse|reverse

```
# Reverse on sequences
[
    "foobar" | reverse,
    ["foo", "bar", "baz"] | toStream | reverse,
    ["foo", "bar", "baz"] | newMutableArray | reverse,
]
>> [["r", "a", "b", "o", "o", "f"], ["baz", "bar", "foo"], ["baz", "bar", "foo"]]
```

### first|first

```
# First on sequences
[
    "foobar" | first,
    ["foo", "bar", "baz"] | toStream | first,
    ["foo", "bar", "baz"] | newMutableArray | first,
]
>> ["f", "foo", "foo"]
```

### running|running

```
# Running on sequences
[
    "bar"
    | running(
        start: "f",
        next: (char, state:) => [state, state, char] | join
    )
    | toArray,
]
>> [
    ["f", "ffb", "ffbffba", "ffbffbaffbffbar"]
]
```

### withIndex|withIndex

```
# With index on sequences
[
    "foobar" | withIndex | toArray,
    ["foo", "bar", "baz"] | toStream | withIndex | toArray,
    ["foo", "bar", "baz"] | newMutableArray | withIndex | toArray,
]
>> [
    [[1, "f"], [2, "o"], [3, "o"], [4, "b"], [5, "a"], [6, "r"]],
    [[1, "foo"], [2, "bar"], [3, "baz"]],
    [[1, "foo"], [2, "bar"], [3, "baz"]],
]
```

### keepFirst|keepFirst

```
# Keep first on sequences
[
    [42, 97, 6, 12, 64] | toStream | keepFirst(3) | toArray,
    [42, 97, 6, 12, 64] | newMutableArray | keepFirst(3) | toArray,
]
>> [[42, 97, 6], [42, 97, 6]]
```

### dropFirst|dropFirst

```
# Drop first on sequences
[
    [42, 97, 6, 12, 64] | toStream | dropFirst(3) | toArray,
    [42, 97, 6, 12, 64] | newMutableArray | dropFirst(3) | toArray,
]
>> [[12, 64], [12, 64]]
```

### slice|slice

```
# Slice on sequences
arr = [42, 97, 6, 12, 64, 73];
[
    arr | toStream | slice(from: 2, to: 4) | toArray,
    arr | newMutableArray | slice(from: 2, to: 4) | toArray,
]
>> [[97, 6, 12], [97, 6, 12]]
```

### while|while

```
# While on sequences
[
    "foobar" | while(| eq("b") | not) | join,
    [1, 2, 1, 1, 3, 4, 1] | while(| lt(3)) | toArray,
    [1, 2, 1, 1, 3, 4, 1] | newMutableArray | while(| lt(3)) | toArray,
]
>> ["foo", [1, 2, 1, 1], [1, 2, 1, 1]]
```

### continueIf|continueIf

```
# Continue-If on sequences
[
    "foobar" | continueIf(| eq("b") | not) | join,
    [1, 2, 1, 1, 3, 4, 1] | continueIf(| lt(3)) | toArray,
    [1, 2, 1, 1, 3, 4, 1] | newMutableArray | continueIf(| lt(3)) | toArray,
]
>> ["foob", [1, 2, 1, 1, 3], [1, 2, 1, 1, 3]]
```

### thenRepeat|thenRepeat

```
# Then repeat on sequences
[
    "foobar" | thenRepeat("na") | keepFirst(9) | join,
    1 | to(3) | thenRepeat(0) | keepFirst(6) | toArray,
    1 | to(3) | newMutableArray | thenRepeat(0) | keepFirst(6) | toArray,
]
>> [
    "foobarnanana",
    [1, 2, 3, 0, 0, 0],
    [1, 2, 3, 0, 0, 0],
]
```

### zip|zip

```
# Zip on sequences
[
    1 | build(| mul(2)) | zip(["one", "two", "three"]) | toArray,
    ["one", "two", "three"] | zip(1 | build(| mul(2))) | toArray,
    [1, 2, 3] | newMutableArray | zip(["one", "two", "three"]) | toArray,
]
>> [
    [[1, "one"], [2, "two"], [4, "three"]],
    [["one", 1], ["two", 2], ["three", 4]],
    [[1, "one"], [2, "two"], [3, "three"]],
]
```

### unzip|unzip

```
# Unzip on sequences
[
    [[1, "one"], [2, "two"], [3, "three"]] | toStream
    | unzip
    | transform(| toArray)
    | toArray,
    [[1, "one"], [2, "two"], [3, "three"]] | newMutableArray
    | unzip
    | transform(| toArray)
    | toArray,
]
>> [
    [[1, 2, 3], ["one", "two", "three"]],
    [[1, 2, 3], ["one", "two", "three"]],
]
```

### flatten|flatten

```
# Flatten on outer sequences
[
    [[1], [2, 3], [4, 5, 6]] | toStream | flatten | toArray,
    [[1], [2, 3], [4, 5, 6]] | newMutableArray | flatten | toArray,
]
>> [
    [1, 2, 3, 4, 5, 6],
    [1, 2, 3, 4, 5, 6],
]
```

```
# Flatten on inner sequences
[[1] | toStream, [2, 3] | newMutableArray, "foo"] | flatten | toArray
>> [1, 2, 3, "f", "o", "o"]
```

### dissect|dissect

```
# Dissect on sequences
[
    "foobarbaz" | dissect(| eq("b")) | transform(join) |toArray,
    [2, 8, 9, 3, 7] | toStream | dissect(| ge(8)) | toArray,
    [2, 8, 9, 3, 7] | newMutableArray | dissect(| ge(8)) | toArray,
]
>> [
    ["foob", "arb", "az"],
    [[2, 8], [9], [3, 7]],
    [[2, 8], [9], [3, 7]],
]
```

### chunk|chunk

```
# Chunk on sequences
[
    "foobarbaz" | chunk(3) | transform(join) | toArray,
    1 | to(9) | toArray | chunk(3) | toArray,
    1 | to(9) | newMutableArray | chunk(3) | toArray,
]
>> [
    ["foo", "bar", "baz"],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
    [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
]
```

### toObject|toObject

```
# Object from sequence of properties
properties = [["foo", "bar"], ["spam", "eggs"]];
[
    properties | toStream | toObject,
    properties | newMutableArray | toObject,
]
>> [{foo: "bar", spam: "eggs"}, {foo: "bar", spam: "eggs"}]
```

### merge|merge

```
# Merge on sequences
[
    [{foo: 1, bar: 2}, {bar: 3, baz: 4}] | toStream | merge,
    [{foo: 1, bar: 2}, {bar: 3, baz: 4}] | newMutableArray | merge,
]
>> [
    {foo: 1, bar: 3, baz: 4},
    {foo: 1, bar: 3, baz: 4},
]
```

### at|at

```
# At on sequences
[
    "foobar" | at(4),
    ["foo", "bar", "baz"] | newMutableArray | at(2),
]
>> ["b", "bar"]
```

### newMutableArray|newMutableArray

```
# Mutable array creation
[
    "foobar" | newMutableArray |.elements(),
    [42, 97, 42, 73] | toStream | newMutableArray |.elements(),
    [42, 97, 42, 73] | newMutableArray | newMutableArray |.elements(),
]
>> [
    ["f", "o", "o", "b", "a", "r"],
    [42, 97, 42, 73],
    [42, 97, 42, 73],
]
```

## Protocol Implementations|implement-protocols

### Set|set-protocols

```
# Set as instance
set = ["foo", "bar", "baz"] | newSet;
[
    set | isInstance,
    classOf(set).name,
    set | display,
]
>> [true, "Set", "Set {elements: [\"foo\", \"bar\", \"baz\"]}"]
```

```
# Set as collection
set = ["foo", "bar", "baz"] | newSet;
[
    set | matches(Collection),
    set | toArray | sort,
    set | toStream | sort,
]
>> [true, ["bar", "baz", "foo"], ["bar", "baz", "foo"]]
```

### Map|map-protocols


```
# Map as instance
map = [["foo", 42], ["bar", 97]] | newMap;
[
    map | isInstance,
    classOf(map).name,
    map | display,
]
>> [true, "Map", "Map {entries: [[\"foo\", 42], [\"bar\", 97]]}"]
```

```
# Map as collection
map = [["foo", 42], ["bar", 97]] | newMap;
[
    map | matches(Collection),
    map | toArray | sort,
    map | toStream | sort,
]
>> [true, [["bar", 97], ["foo", 42]], [["bar", 97], ["foo", 42]]]
```

### MutableArray|mutable-array-protocols

```
# Mutable array as instance
array = ["foo", "bar", "baz"] | newMutableArray;
[
    array | isInstance,
    classOf(array).name,
    array | display,
]
>> [true, "MutableArray", "MutableArray {elements: [\"foo\", \"bar\", \"baz\"]}"]
```

```
# Mutable array as sequence
array = ["foo", "bar", "baz"] | newMutableArray;
[
    array | isSequence,
    array | toArray,
    array | toStream | toArray,
    array @ 2,
    array | at(4, default: $ "boo"),
]
>> [true, ["foo", "bar", "baz"], ["foo", "bar", "baz"], "bar", "boo"]
```

### MutableSet|mutable-set-protocols

```
# Mutable set as instance
set = ["foo", "bar", "baz"] | newMutableSet;
[
    set | isInstance,
    classOf(set).name,
    set | display,
]
>> [true, "MutableSet", "MutableSet {elements: [\"foo\", \"bar\", \"baz\"]}"]
```

```
# Mutable set as collection
set = ["foo", "bar", "baz"] | newMutableSet;
[
    set | matches(Collection),
    set | toArray | sort,
    set | toStream | sort,
]
>> [true, ["bar", "baz", "foo"], ["bar", "baz", "foo"]]
```

### MutableMap|mutable-map-protocols



```
# Mutable map as instance
map = [["foo", 42], ["bar", 97]] | newMutableMap;
[
    map | isInstance,
    classOf(map).name,
    map | display,
]
>> [true, "MutableMap", "MutableMap {entries: [[\"foo\", 42], [\"bar\", 97]]}"]
```

```
# Mutable map as collection
map = [["foo", 42], ["bar", 97]] | newMutableMap;
[
    map | matches(Collection),
    map | toArray | sort,
    map | toStream | toArray | sort,
]
>> [true, [["bar", 97], ["foo", 42]], [["bar", 97], ["foo", 42]]]
```
