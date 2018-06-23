<!-- TOC -->

- [Programming Elixir](#programming-elixir)
    - [Part1 Conventional Programming](#part1-conventional-programming)
        - [Pattern Matching](#pattern-matching)
        - [Immutability](#immutability)
        - [Elixir Basics](#elixir-basics)
            - [All around](#all-around)
            - [Atoms](#atoms)
            - [Tuples](#tuples)
            - [Lists](#lists)
        - [Keyword lists](#keyword-lists)
            - [Maps](#maps)
        - [Variable Scope](#variable-scope)
            - [`with` expression](#with-expression)

<!-- /TOC -->

# Programming Elixir

## Part1 Conventional Programming

### Pattern Matching

Match operator `=` is not assignment. It's an assertion. It's true if Elixir can find a way of making the left-hand side equal the right-hand side. If the left-hand side is variable, Elixir will bind the right value to the left. `^` can force Elixir compare left-hand side and right-hand side using the existing value.

```
iex> a = 1
1
iex> 1 = a
1
iex> 2 = a
** (MatchError) no match of right hand side value: 1
iex> ^a = 2
** (MatchError) no match of right hand side value: 2
```

`_` is the value being ignored
```
iex> [1, _, _] = [1, 2, 3]
```

### Immutability

All values are immutable. If you want to add 100 to [1,2,3], Elixir produces a copy of the original, containing the new values.


In the following example, Elixir knows list1 will never chang, so it simply create a new list with head of `4` and a tail of `list1`.
```
iex> list1 = [ 3, 2, 1 ] [3, 2, 1]
iex> list2 = [ 4 | list1 ] [4, 3, 2, 1]
```

### Elixir Basics

#### All around

Elixir 可以比較任意兩種型別

`number < atom < reference < function < port < pid < tuple < map < list < bitstring`

#### Atoms

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

#### Tuples

Similar to lists, continuous in memory, fast in accessing its length, slow in modificating it (most similar to array)

`{}`

Tuples usually are additional function returning messages

```
iex> { :ok, file } = File.open("mix.exs")
{:ok, #PID<0.39.0>}
iex> { :ok, file } = File.open("non-existent-file")
** (MatchError) no match of right hand side value: {:error, :enoent}
```

#### Lists

Counting length is O(n), prepend is faster than append

`[]`

List concatenation
```
iex> list1 = [1, :foo, "bar"]
iex> list2 = [2]
iex> list2 ++ list1
[2, 1, :foo, "bar"]
```

List subtraction (difference), for each element on the right, the same element that exists in the first will be eliminated
```
iex> [1,2,3,1] -- [1,2]
[3,1]
```

List membership
```
iex> 1 in [1,2,3]
true
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

### Keyword lists

A special list of two-element tuples, the first element in the tuple is atom, performance is as same as lists

```
# {} of tuple can be omitted
iex> [foo: "bar", hello: "world"]
iex> [{:foo, "bar"}, {:hello, "world"}]
iex> [{:foo, "bar"}, {:hello, "world"}, {:hello, "kitty"}]
```

1. Keys are atoms
2. Keys are ordered
3. Keys may not be unique

Keyword lists are most commonly used to pass options to functions

#### Maps

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

1. Key is unique
2. Efficient
3. Used for pattern matching

*In general, use keyword lists for things such as command-line parameters and for passing around options, and use maps when you want an associative array.*

### Variable Scope

Elixir is lexically scoped.

#### `with` expression

1. Defines a local scope for variables
2. Give control over pattern matching

The variables inside `with` are local.

If any pattern matching inside `with` fails, it returns the value that couldn't be matched.

```
# The value of the with is the value of its do parameter.
lp = with some_pattern_matching,
          another_pattern_matching,
          yet_another_pattern_matching
     do
        ...
     end
```





