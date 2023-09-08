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
```