# Teach Myself Elixir

This is brief introduction to the Elixir language through samples of its main features.

## String

### String Interpolation

```elixir
iex> name = "Sean"
"Sean"
iex> "Hello #{name}"
"Hello Sean"
```

### String Concatenation

```elixir
iex> name = "Sean"
"Sean"
iex> "Hello " <> name
"Hello Sean"
```

## Collections

```elixir
# List
# List are simple collections of values which may include multiple types.
# List may also include non-unique values.
# Elixir implements list collections as linked list.

iex> [3.14, :pie, "Apple"]
[3.14, :pie, "Apple"]
iex> list = [3.14, :pie, "Apple"]

# Prepending
iex> ["a" | list]
["a", 3.14, :pie, "Apple"]

# Appending
iex> list ++ ["Cherry"]
[3.14, :pie, "Apple", "Cherry"]

# List substraction uses strict comparison to match the values
iex> ["foo", :bar, 42] -- [42, "bar"]
["foo", :bar]
iex> [1, 2, 2, 3, 2, 3] -- [1,2,3,2]
[2, 3]

# Head is the list's fist element
iex> hd [3.14, :pie, "Apple"]
3.14

# Tail is a list that contains all of the remaining elements except the first.
iex> tl [3.14, :pie, "Apple"]
[:pie, "Apple"]

# Tuples are similar to list, but are stored contiguosly in memory.
# It's common for tuples to be used as a mechanism to return additional information from functions
iex > {3.14, :pie, "Apple"}
{3.14, :pie, "Apple"}

# Keyword lists is a special list of two-element typles whose first element is atom
# Keyword list share performance with list
# Keyword list are most commonly used to pass options to functions
iex> [foo: "bar", hello: "world"]
[foo: "bar", hello: "world"]
iex> [{:foo, "bar"}, {:hello, "world"}]
[foo: "bar", hello: "world"]
# keys do not have to be unique
iex> [{:foo, "bar"}, {:hello, "world"}, {:foo, "baz"}]
[foo: "bar", hello: "world", foo: "baz"]

# Maps are key-value store.
# Unlike keyword list, they allow keys of any type and are un-ordered
# You can define a map with `%{}` syntax
iex> map = %{:foo => "bar", "hello" => :world}
%{:foo => "bar", "hello" => :world}
## Access value of map
iex> map[:foo]
"bar"
iex> map["hello"]
:world
# There is another syntax to create map and access the value of map.
iex> map = %{foo: "bar", hello: "world"}
%{foo: "bar", hello: "world"}
iex> map.hello
"world"
# To create new key
iex> Map.put(map, :buzz, "word")
%{foo: "bar", hello: "world", buzz: "word"}
# To update value. If key doesn't exist, a KeyError will be raised
iex> %{map | hello: "sunshine"}
%{foo: "bar", hello: "sunshine"}
```

## Function

```elixir
# Anonymous Function
iex> sum = fn(a,b) -> a+b end
iex> sum.(2,3)
5
# & shorthand for anonymous function
iex> sub = &(&1 - &2)
iex> sub.(2,1)

# Elixir uses pattern matching to check through all possible match options and
# select the first matching option to run
iex> handle_result = fn
...>     {:ok, result} -> IO.puts "Result: #{result}"
...>     {:error} -> IO.puts "An error has occured!"
...> end
iex> handle_result.({:ok, "success"})
Result: success
:ok
iex> handle_result.({:error})
An error has occured!
:ok

# Function
iex> defmodule Greeter do
...>     def hello(name) do
...>         "Hello, " <> name
...>     end
...> end
iex> Greeter.hello("Sean")
"Hello, Sean"

# If our function body only spans one line, we can shorten it with `do:`
iex> defmodule Greeter do
...>     def hello(name), do: "Hello, " <> name
...> end

# Recursion with pattern matching
iex> defmodule Length do
...>     def of([]), do: 0
...>     def of([_ | tail]), do: 1 + of(tail)
...> end
iex> Length.of([])
0
iex> Length.of([1,4,3])
3

# Arity (number of arguments)
iex> defmodule Greeter2 do
...>     def hello(), do: "Hello, anonymous person!"                    # hello/0
...>     def hello(name), do: "Hello, " <> name                         # hello/1
...>     def hello(name1, name2), do: "Hello, #{name1} and #{name2}"    # hello/2
...> end
iex> Greeter2.hello()
"Hello, anonymous person!"
iex> Greeter2.hello("Sunshine")
"Hello, Sunshine"
iex> Greeter2.hello("Sunshine", "Sugar")
"Hello, Sunshine and Sugar"

# Function and Pattern Matching
defmodule Greeter3 do
    def hello(%{name: person_name}) do
        IO.puts "Hello, " <> person_name
    end
end

fred = %{name: "Fred", age: "95", favorite_color: "Taupe"}

iex> Greeter3.hello(fred)
Hello, Fred

defmodule Greeter4 do
    def hello(%{name: person_name} = person) do
        IO.puts "Hello, " <> person_name
        IO.inspect person
    end
end

iex> Greeter4.hello(fred)
Hello, Fred
%{name: "Fred", age: "95", favorite_color: "Taupe"}

# Private Functions
defmodule Greeter5 do
    def hello(name), do: phrase() <> name
    defp phrase, do: "Hello, "
end

iex> Greeter5.hello("James")
"Hello, James"

# Guards
defmodule Greeter6 do
    def hello(names) when is_list(names) do
        names = Enum.join(names, ", ")

        hello(names)
    end

    def hello(name) when is_binary(name) do
        phrase() <> name
    end

    defp phrase, do: "Hello, "
end

iex> Greeter6.hello ["Sean", "Steve"]
"Hello, Sean, Steve"

# Default Arguments
defmodule Greeter7 do
    def hello(name, language_code \\ "en") do
        phrase(language_code) <> name
    end

    defp phrase("en"), do: "Hello, "
    defp phrase("es"), do: "Hola, "
end

iex> Greeter7.hello("Sean", "en")
"Hello, Sean"
iex> Greeter7.hello("Sean")      
"Hello, Sean"
iex> Greeter7.hello("Sean", "es")
"Hola, Sean"

# Elixir doesn't like default arguments in multiple matching functions, 
# it can be confusing. To handle this we can add a function head with our
# default arguments:
defmodule Greeter8 do
    def hello(names, language_code \\ "en")
    def hello(names, language_code) when is_list(names) do
        names = Enum.join(names, ", ")
        hello(names, language_code)
    end

    def hello(name, language_code) when is_binary(name) do
        phrase(language_code) <> name
    end

    defp phrase("en"), do: "Hello, "
    defp phrase("en"), do: "Hola, "
end

iex> Greeter8.hello(["Sean", "Steve"])
"Hello, Sean, Steve"

```
