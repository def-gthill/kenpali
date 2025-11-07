# Kenpali Semantic Specification

## Names

It's an error to declare the same name more than once in the same block.

```
# Duplicate name declaration
foo = 42;
foo = 97;
foo
!! duplicateName {"name": "foo"}
```

A block creates a _scope_; all names defined within it are only accessible within it.

```
# Scope
foo = (
    bar = 42;
    null
);
bar
!! nameNotDefined {"name": "bar"}
```

But expressions in a block can reference names defined in a containing block.

```
# A name from an enclosing scope
foo = 42;
(
    bar = 73;
    foo
)
>> 42
```

A block can define a name that duplicates one in an enclosing block. References to that name evaluate to the _innermost_ accessible version of the name—the inner name _shadows_ the outer one.

```
# Shadowing
foo = 42;
(
    foo = 73;
    foo
)
>> 73
```

Name definitions are processed in the order in which they appear in the block. If a name reference is evaluated before the name's definition has been processed, a runtime error occurs.

```
# Name used before assignment
foo = baz;
bar = 42;
baz = bar;
foo
!! nameUsedBeforeAssignment {"name": "baz"}
```

However, names are in scope across the entire block in which they are defined. A name still shadows an outer definition, even if it hasn't been assigned yet.

```
# Name shadowing before assignment
foo = 42;
(
    bar = foo;
    foo = 73;
    bar
)
!! nameUsedBeforeAssignment {"name": "foo"}
```

## Strings

```
# String length counts Unicode code points
"foo\u{1f61b}" | length
>> 4
```

```
# String indexing counts Unicode code points
"foo\u{1f61b}bar" @ 5
>> "b"
```

```
# String indexing from the end counts Unicode code points
"bar\u{1f61b}foo" @ -5
>> "r"
```

```
# String streaming iterates over Unicode code points
"foo\u{1f61b}" | toStream | length
>> 4
```

```
# Malformed string literal
"foo\u{1f61b"
!! invalidStringLiteral {"value": "\"foo\\u{1f61b\""}
```

## Arrays

Arrays can freely mix different types of elements.

```
# Array with mixed types
[null, 1, "foo"]
>> [null, 1, "foo"]
```

```
# Nested arrays
[1, [2, [3, 4]]]
>> [1, [2, [3, 4]]]
```

```
# Destructuring an array with an object pattern
{foo:, bar:} = [1, 2];
foo
!! wrongType {"value": [1, 2], "expectedType": "either(Object, Instance)"}
```

Excess elements in an array pattern are ignored.

```
# Excess elements in an array pattern
[foo, bar, baz] = [1, 2, 3, 4];
[foo, bar, baz]
>> [1, 2, 3]
```

Missing elements in an array pattern cause a runtime error.

```
# Missing elements in an array pattern
[foo, bar, baz] = [1, 2];
[foo, bar, baz]
!! missingElement {"value": [1, 2], "name": "baz"}
```

Array destructuring can be nested.

```
# Nested array destructuring
[foo, [bar, baz]] = [1, [2, 3]];
[foo, bar, baz]
>> [1, 2, 3]
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

## Objects

Objects can freely mix different types of values.

```
# Object with mixed types
{foo: null, bar: 1, baz: [2]}
>> {foo: null, bar: 1, baz: [2]}
```

```
# Nested objects
{foo: {bar: "baz"}}
>> {foo: {bar: "baz"}}
```

```
# Destructuring an object with an array pattern
[foo, bar] = {foo: 42, bar: 97};
foo
!! wrongType {"value": {"foo": 42, "bar": 97}, "expectedType": "either(Array, Stream)"}
```

```
# Nested object destructuring
{foo: {bar: baz}} = {foo: {bar: 42}};
baz
>> 42
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
    2 | build(| mul(2)) @ 2,
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
[foo, bar, baz] = 1 | build(| mul(2));
[bar, baz, foo]
>> [2, 4, 1]
```

```
# Destructuring a stream with rest
[foo, *rest] = 1 | build(| mul(2));
[foo, rest | keepFirst(3) | toArray]
>> [1, [2, 4, 8]]
```

```
# Destructuring a stream with a middle rest
[foo, *rest, bar] = 1 | build(| mul(2)) | while(| lt(100));
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
answer = newVar(42);
stream = 1 | to(3) | transform(| add(answer.get()));
before = stream | toArray;
answer.set(73);
after = stream | toArray;
[before, after]
>> [[43, 44, 45], [43, 44, 45]]
```

```
# Display values in streams
stream = [newVar(42)] | toStream;
stream | toArray;
stream | display
>> "Stream [Var {value: 42}]"
```

## Defining and Calling Functions

```
# Calling something that isn't a function
42()
!! notCallable {"value": 42}
```

```
# No parameters, no arguments
foo = $ 42;
foo()
>> 42
```

```
# Positional and named parameters and arguments
foo = (a, b:) => [a, b];
foo(42, b: 97)
>> [42, 97]
```

```
# Missing positional argument
foo = (a, b:) => [a, b];
foo(b: 97)
!! missingArgument {"name": "a"}
```

```
# Missing named argument
foo = (a, b:) => [a, b];
foo(42)
!! missingArgument {"name": "b"}
```

Positional and named parameters are strictly separated; trying to pass a positional argument to a named parameter or vice versa causes a runtime error.

```
# Named parameter passed as positional
foo = (a, b:) => [a, b];
foo(42, 97)
!! missingArgument {"name": "b"}
```

```
# Positional parameter passed as named
foo = (a, b:) => [a, b];
foo(a: 42, b: 97)
!! missingArgument {"name": "a"}
```

Interleaving named and positional parameters is unconventional but legal.

```
# Interleaving named and positional parameters
foo = (a, b:, c, d:) => [a, b, c, d];
foo(42, 73, b: 97, d: 216)
>> [42, 97, 73, 216]
```

So is interleaving named and positional arguments.

```
# Interleaving named and positional arguments
foo = (a, b, c:, d:) => [a, b, c, d];
foo(42, c: 73, 97, d: 216)
>> [42, 97, 73, 216]
```

```
# Optional positional parameter, argument supplied
foo = (a, b = 1) => [a, b];
foo(42, 73)
>> [42, 73]
```

```
# Optional positional parameter, no argument supplied
foo = (a, b = 1) => [a, b];
foo(42)
>> [42, 1]
```

```
# Default value referencing a name
foo = 73;
bar = (x = foo) => x;
bar()
>> 73
```

```
# Mutable default value
foo = (x = newVar(42)) => (
    x.get() | up | (x.set);
    x.get()
);
[foo(), foo(), foo()]
>> [43, 43, 43]
```

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

A _positional rest parameter_ collects all remaining positional arguments into an array.

```
# Positional rest parameter
foo = (*args) => args;
foo(42, 97, 216)
>> [42, 97, 216]
```

Ordinary positional parameters can go before and after a positional rest parameter.

```
# Positional rest parameter and ordinary parameters
foo = (a, *args, b) => [a, args, b];
foo(42, 73, 97, 216)
>> [42, [73, 97], 216]
```

Arrays can be spread to fill positional parameters.

```
# Spread positional arguments
foo = (a, b, c, d) => [d, c, b, a];
x = [42, 73];
y = [97, 216];
foo(*x, *y)
>> [216, 97, 73, 42]
```

```
# Named rest parameter
foo = (**args) => args;
foo(bar: 42, baz: 97)
>> {bar: 42, baz: 97}
```

```
# Named rest parameter and ordinary parameters
foo = (a:, **args, b:) => [a, args, b];
foo(a: 42, foo: 73, bar: 97, b: 216)
>> [42, {foo: 73, bar: 97}, 216]
```

```
# Spread named arguments
foo = (a:, b:, c:, d:) => [d, c, b, a];
x = {a: 42, c: 97};
y = {b: 73, d: 216};
foo(**x, **y)
>> [216, 97, 73, 42]
```

Defining multiple rest parameters of the same type is ambiguous and causes a runtime error.

```
# Multiple positional rest parameters
(*a, *b) => [a, b]
!! overlappingRestPatterns {"names": ["a", "b"]}
```

```
# Multiple named rest parameters
(**a, **b) => [a, b]
!! overlappingRestPatterns {"names": ["a", "b"]}
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
# Destructuring in parameters
foo = ([a, b]) => [b, a];
foo([42, 97])
>> [97, 42]
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

A function can reference names defined in an enclosing scope.

```
# Capturing a name from an enclosing scope
x = 73;
foo = (y) => [x, y];
foo(42)
>> [73, 42]
```

A function can capture a name defined _below_ it, as long as the function isn't _called_ until after the definition has been processed.

```
# Capturing a name defined below
foo = () => bar;
bar = 42;
foo()
>> 42
```

```
# Capturing a name defined below, calling too early
foo = $ bar;
baz = foo();
bar = 42;
baz
!! nameUsedBeforeAssignment {"name": "bar"}
```

A function can reference names that were in scope when the function was _defined_, even if those names are out of scope when the function is _called_.

```
# Closure
foo = $ (
    x = 73;
    (y) => [x, y]
);
foo()(42)
>> [73, 42]
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

Names that are in scope when the function is called don't leak into the function body.

```
# Leakage
leaky = $ intruder;
(
    intruder = 42;
    leaky()
)
!! nameNotDefined {"name": "intruder"}
```

## Indexing

```
# Indexing strings - wrong index type
"foobar" @ "baz"
!! wrongType {"value": "baz", "expectedType": "Number"}
```

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

```
# Indexing arrays
["foo", "bar"] @ 2
>> "bar"
```

```
# Indexing arrays - index from end
["foo", "bar"] @ -2
>> "foo"
```

```
# Indexing arrays - wrong index type
["foo", "bar"] @ "baz"
!! wrongType {"value": "baz", "expectedType": "Number"}
```

```
# Indexing arrays - index less than minus length
["foo", "bar"] @ -3
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": -3}
```

```
# Indexing arrays - index 0
["foo", "bar"] @ 0
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 0}
```

```
# Indexing arrays - index greater than length
["foo", "bar"] @ 3
!! indexOutOfBounds {"value": ["foo", "bar"], "length": 2, "index": 3}
```

```
# Indexing objects
{foo: "bar", spam: "eggs"} @ "spam"
>> "eggs"
```

```
# Indexing objects - wrong index type
{foo: "bar", spam: "eggs"} @ 42
!! wrongType {"value": 42, "expectedType": "String"}
```

```
# Indexing objects - property not in object
{foo: "bar", spam: "eggs"} @ "baz"
!! missingProperty {"value": {"foo": "bar", "spam": "eggs"}, "key": "baz"}
```

```
# Indexing something non-indexable
42 @ 1
!! wrongType {"value": 42, "expectedType": "either(Sequence, Object, Instance)"}
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
# Instances as set members
var1 = newVar(42);
var2 = newVar(42);
set = [var1] | newSet;
[set.has(var1), set.has(var2)]
>> [true, false]
```

```
# Set-has as callback
set = [1, 2, 3, 4, 42] | newSet;
[3, 8, 4, 42, 57] | where(set.has) | toArray
>> [3, 4, 42]
```

## Errors

If an expression throws an error, that error propagates outward through enclosing expressions, aborting further evaluation.

```
# Error short-circuiting through function calls
($ 42)(([foo] = []; foo))
!! missingElement {"value": [], "name": "foo"}
```

```
# Error short-circuiting through arrays
[([foo] = []; foo)]
!! missingElement {"value": [], "name": "foo"}
```

```
# Error short-circuiting through objects
{bar: ([foo] = []; foo)}
!! missingElement {"value": [], "name": "foo"}
```

```
# Error thrown after catching
try($ 1 @ 1, onError: itself) @ 1
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
  try(baz, onError: |.calls)
);
main()
>> [
    {function: "$main/bar/$anon1"},
    {function: "$main/bar"},
    {function: "$main/foo"},
    {function: "$main/main/baz"},
]
```

```
# Stack traces through platform functions
foo = $ 1 | to(3) | forEach($ 1 @ 1);
try(foo, onError: |.calls)
>> [
    {function: "$main/foo/$anon1"},
    {function: "forEach"},
    {function: "$main/foo"},
]
```

## Variables

```
# Simultaneous variables
a = newVar(42);
b = newVar(97);
a.set(73);
[a.get(), b.get()]
>> [73, 97]
```
