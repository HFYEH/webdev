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
        - [Anonymous Functions](#anonymous-functions)
        - [Modules and Named Functions](#modules-and-named-functions)
            - [Guard Clauses](#guard-clauses)
            - [Default Parameters](#default-parameters)
            - [The |> Operator](#the--operator)
            - [Modules](#modules)
                - [Directive](#directive)
            - [Module Attributes](#module-attributes)
            - [Module Names](#module-names)
            - [Calling a Function in an Erlang Library](#calling-a-function-in-an-erlang-library)

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

#### Keyword lists

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

### Anonymous Functions

```
fn
  paramter-list -> body
  paramter-list -> body
end

iex> sum = fn (a, b) -> a + b end
# The dot indicates the function call
iex> sum.(1,2)
3
```

When making a function call, it do pattern matching to the input arguments with parameter list.
And only the body that has input matched parameter list will be returned.

Function can also return function. A closure.

```
iex> greeter = fn name -> (fn -> "Hello #{name}" end) end
iex> dave_greeter = greeter.("Dave")
iex> dave_greeter.()
"Hello Dave"

iex> add_one = &(&1 + 1) # same as add_one = fn (n) -> n + 1 end
```

If the body of anonymous function is just a call to named function, Elixir replaces it with a direct reference to that function. (Arguements must in order)

```
iex> rnd = &(Float.round(&1, &2))
&Float.round/2
```

### Modules and Named Functions

All of the following are the same, `do...end` is just syntactic sugur of `do:`, and `do:` is just a term in a keyword list
```
def xxx(), do: ooo

def xxx, do: (
    ooo
)

def xxx do
  ooo
end
```

Elixir tries functions from the top down, executing the first match.
```
defmodule BadFactorial do
  def of(0), do: 1
  def of(n), do: n * of(n-1)
end

defmodule BadFactorial do
  def of(n), do: n * of(n-1)
  def of(0), do: 1
end
```

#### Guard Clauses

```
defmodule Guard do
  def what_is(x) when is_number(x) do
    IO.puts "#{x} is a number"
  end
  def what_is(x) when is_list(x) do
    IO.puts "#{inspect(x)} is a list"
  end
  def what_is(x) when is_atom(x) do
    IO.puts "#{x} is an atom"
  end
end
```

#### Default Parameters

Simply add a function head with no body that contains the default parameters, and use regular parameters for the rest. The defaults will apply to all calls to the function.

```
defmodule Params do
  def func(p1, p2 \\ 123)
  def func(p1, p2) when is_list(p1) do
    "You said #{p2} with a list"
  end
  def func(p1, p2) do
    "You passed in #{p1} and #{p2}"
  end
end
```

#### The |> Operator

The `|>` operator takes the result of the expression to its left and inserts it as the first parameter of the function invocation to its right.

Always use `()` in `|>`.

#### Modules

##### Directive

Lexically scoped.

`import Module [, only:|except: ]`
```
defmodule Example do
  def func1 do
    List.flatten [1,[2,3],4]
  end
  def func2 do
    import List, only: [flatten: 1]
    flatten [5,[6,7],8]
  end 
end
```

alias
```
defmodule Example do
  def compile_and_go(source) do
    alias My.Other.Module.Parser, as: Parser
    alias My.Other.Module.Runner, as: Runner
    source
      |> Parser.parse()
      |> Runner.execute()
  end
end
```

require

You require a module if you want to use any macros it defines.

#### Module Attributes

Works only at the top level of a module.
```
defmodule Example do
  @example "My example"
end
```

These attributes are not variables in the conventional sense. Use them for configuration and metadata only. (Similar to Ruby constants.)

#### Module Names

Module names are atoms. Elixir auto converts upper case `IO` to atom `Elixir.IO`.


#### Calling a Function in an Erlang Library

Erlang variables start with an uppercase letter and atoms are lowercase names.

Such as `:io`, `:timer`.


