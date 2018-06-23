<!-- TOC -->

- [Programming Elixir](#programming-elixir)
    - [Conventional Programming](#conventional-programming)
        - [Pattern Matching](#pattern-matching)
        - [Immutability](#immutability)
    - [Elixir Basics](#elixir-basics)
        - [All around](#all-around)
        - [Atoms](#atoms)
        - [Lists](#lists)
        - [Tuples](#tuples)
        - [Keyword lists (associative collections)](#keyword-lists-associative-collections)
        - [Maps (associative collections)](#maps-associative-collections)

<!-- /TOC -->

# Programming Elixir

## Conventional Programming

### Pattern Matching

### Immutability



## Elixir Basics

### All around

Elixir 可以比較任意兩種型別

`number < atom < reference < function < port < pid < tuple < map < list < bitstring`

### Atoms

Atom (symbol in Ruby): `:foo`

Boolean is atom
```
iex> true |> is_boolean
true
iex> true |> is_atom
true
```

Module name is atom
```
iex> is_atom(MyApp.MyModule)
true
```

### Lists

Counting length is O(n), prepend is faster than append

`[]`

List concatenation
```
iex> list1 = [1, :foo, "bar"]
iex> list2 = [2]
iex> list2 ++ list1
[2, 1, :foo, "bar"]
```

List subtraction, for each element on the right, the same element that exists in the first will be eliminated
```
iex> [1,2,3,1] -- [1,2]
[3,1]
```

head and tail
```
iex> hd [3.14, :pie, "Apple"]
3.14
iex> tl [3.14, :pie, "Apple"]
[:pie, "Apple"]

# Pattern matching and | operator
iex> [head | tail] = [3.14, :pie, "Apple"]

iex> [1,2,3] ++ 4
[1, 2, 3 | 4]
```

### Tuples

Similar to lists, continuous in memory, fast in accessing its length, slow in modificating it

`{}`

Tuples usually are additional function returning messages

### Keyword lists (associative collections)

A special list of two-element tuples, the first element in the tuple is atom, performance is as same as lists

```
iex> [{:foo, "bar"}, {:hello, "world"}]
iex> [{:foo, "bar"}, {:hello, "world"}, {{:hello, "kitty"}}]
```

1. Keys are atoms
2. Keys are ordered
3. Keys may not be unique

Keyword lists are most commonly used to pass options to functions

### Maps (associative collections)

A key-value pair, no restriction on data type and un-orderd

`%{}`

```
iex> map = %{:foo => "bar", "hello" => :world}
iex> map.foo
"bar"
iex> map[:foo]
"bar"
iex> map["hello]
:world
iex> %{map | foo: "baz"}
%{:foo => "baz", "hello" => :world}
```