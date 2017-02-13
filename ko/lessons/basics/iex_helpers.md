---
layout: page
title: IEx 헬퍼
category: basics
order: 16
lang: ko
---

{% include toc.html %}

## 개요

Elixir를 다루기 시작할 때부터, IEx는 우리들의 가장 좋은 친구입니다.
IEx는 일종의 REPL이지만, 새로운 코드를 둘러보거나 여러분만의 작업물을 만들 때든 언제든지 삶을 편안하게 만들어줄 수 있는 고급 기능을 많이 가지고 있습니다.
이번 수업에서는 IEx에 내장되어 있는 여러 헬퍼들을 살펴보도록 하겠습니다.

### 자동 완성

셸에서 작업을 하는 도중에는, 잘 사용해본 적이 없는 모듈을 다뤄볼 일이 생기곤 합니다.
자동 완성 기능은 이런 사용해본 적 없는 모듈에 어떤 것을 사용할 수 있는 지 알아볼 때 굉장히 유용합니다.
모듈의 이름 뒤에 `.`을 누르고 `Tab`키를 누르면

```elixir
iex> Map. # Tab 키를 누르면
delete/2             drop/2               equal?/2
fetch!/2             fetch/2              from_struct/1
get/2                get/3                get_and_update!/3
get_and_update/3     get_lazy/3           has_key?/2
keys/1               merge/2              merge/3
new/0                new/1                new/2
pop/2                pop/3                pop_lazy/3
put/3                put_new/3            put_new_lazy/3
split/2              take/2               to_list/1
update!/3            update/4             values/1
```

사용할 수 있는 함수의 이름과 애리티를 확인할 수 있어요!

### `.iex.exs`

IEx가 실행될 때마다 IEx는 .iex.exs 설정 파일을 확인합니다. 현재 디렉터리에서 해당 파일을 찾지 못할 경우에는, 사용자의 홈 디렉터리에 있는 파일(~/.iex.exs)를 대신 사용합니다.

Configuration options and code defined within this file will be available to us when the IEx shell starts up. For instance if we want some helper functions available to us in IEx, we can open up .iex.exs and make some changes.

헬퍼 메서드가 몇 개 있는 모듈을 추가하는 것부터 시작해봅시다.

```elixir
defmodule IExHelpers do
  def whats_this?(term) when is_nil(term), do: "타입: Nil"
  def whats_this?(term) when is_binary(term), do: "타입: 이진"
  def whats_this?(term) when is_boolean(term), do: "타입: 불리언"
  def whats_this?(term) when is_atom(term), do: "타입: 애텀"
  def whats_this?(_term), do: "타입: 알 수 없음"
end
```

이제 IEx를 시작할 때마다 IExHelpers 모듈을 바로 사용할 수 있습니다. IEx를 열어서 방금 만들어 본 헬퍼를 사용해 보도록 합시다.

```elixir
$ iex
{{ site.erlang.OTP }} [{{ site.erlang.erts }}] [source] [64-bit] [smp:8:8] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

Interactive Elixir ({{ site.elixir.version }}) - press Ctrl+C to exit (type h() ENTER for help)
iex> IExHelpers.whats_this?("a string")
"Type: 이진 값"
iex> IExHelpers.whats_this?(%{})
"Type: 알 수 없음"
iex> IExHelpers.whats_this?(:test)
"Type: 애텀"
```
위에서 한 대로, 헬퍼를 불러오기 위해서 따로 require나 import 할 필요가 없습니다. IEx가 대신 해 주는 것이지요.

### `h`

Elixir 셸이 제공해주는 도구 중 가장 유용한 것을 고르자면 `h` 이겠지요.
Due to the language's fantastic first class support for documentation, the docs for any code can be reached using this helper.
간단하게 직접 사용해볼 수 있습니다.

```elixir
iex> h Enum
                                      Enum

Provides a set of algorithms that enumerate over enumerables according to the
Enumerable protocol.

┃ iex> Enum.map([1, 2, 3], fn(x) -> x * 2 end)
┃ [2, 4, 6]

Some particular types, like maps, yield a specific format on enumeration. For
example, the argument is always a {key, value} tuple for maps:

┃ iex> map = %{a: 1, b: 2}
┃ iex> Enum.map(map, fn {k, v} -> {k, v * 2} end)
┃ [a: 2, b: 4]

Note that the functions in the Enum module are eager: they always start the
enumeration of the given enumerable. The Stream module allows lazy enumeration
of enumerables and provides infinite streams.

Since the majority of the functions in Enum enumerate the whole enumerable and
return a list as result, infinite streams need to be carefully used with such
functions, as they can potentially run forever. For example:

┃ Enum.each Stream.cycle([1, 2, 3]), &IO.puts(&1)
```

여기서 더 나아가 셸 안에서 자동 완성 기능과 함께 사용할 수도 있습니다.
처음으로 Map에 관해 찾아본다고 생각해보세요.

```elixir
iex> h Map
                                      Map

A set of functions for working with maps.

Maps are key-value stores where keys can be any value and are compared using
the match operator (===). Maps can be created with the %{} special form defined
in the Kernel.SpecialForms module.

iex> Map.
delete/2             drop/2               equal?/2
fetch!/2             fetch/2              from_struct/1
get/2                get/3                get_and_update!/3
get_and_update/3     get_lazy/3           has_key?/2
keys/1               merge/2              merge/3
new/0                new/1                new/2
pop/2                pop/3                pop_lazy/3
put/3                put_new/3            put_new_lazy/3
split/2              take/2               to_list/1
update!/3            update/4             values/1

iex> h Map.merge/2
                             def merge(map1, map2)

Merges two maps into one.

All keys in map2 will be added to map1, overriding any existing one.

If you have a struct and you would like to merge a set of keys into the struct,
do not use this function, as it would merge all keys on the right side into the
struct, even if the key is not part of the struct. Instead, use
Kernel.struct/2.

Examples

┃ iex> Map.merge(%{a: 1, b: 2}, %{a: 3, d: 4})
┃ %{a: 3, b: 2, d: 4}
```

As we can see we were not only able to find what functions were available as part of the module but we were able to access individual function docs, many of which include example usage.

### `i`

Let's put some of our new found knowledge to use by employing `h` to learn a bit more about the `i` helper:

```elixir
iex> h i

                                  def i(term)

Prints information about the given data type.

iex> i Map
Term
  Map
Data type
  Atom
Module bytecode
  /usr/local/Cellar/elixir/1.3.3/bin/../lib/elixir/ebin/Elixir.Map.beam
Source
  /private/tmp/elixir-20160918-33925-1ki46ng/elixir-1.3.3/lib/elixir/lib/map.ex
Version
  [9651177287794427227743899018880159024]
Compile time
  no value found
Compile options
  [:debug_info]
Description
  Use h(Map) to access its documentation.
  Call Map.module_info() to access metadata.
Raw representation
  :"Elixir.Map"
Reference modules
  Module, Atom
```

Now we have a bunch of information about Map including where its source is stored and the modules it references.  This is quite useful when exploring custom, foreign data types, and new functions.

The individual headings can be dense, but at a high level we can gather some relevant information:

- Its an atom data type
- Where the source code is
- The version, and compile options
- A general description
- How to access it
- What other modules it references

This gives us a lot to work with and is better than going in blind.

### `r`

If we want to recompile a particular module we can use the `r` helper.  Let's say we've changed some code and want to run a new function we've added. To do that we need to save our changes and recompile with r:

```elixir
iex> r MyProject
warning: redefining module MyProject (current version loaded from _build/dev/lib/my_project/ebin/Elixir.MyProject.beam)
  lib/my_project.ex:1

{:reloaded, MyProject, [MyProject]}
```

### `s`

With `s` we can retrieve the type spec information for a  module or function, we can use this to know it expects:

```elixir
iex> s Map.merge/2
@spec merge(map(), map()) :: map()

# it also works on entire modules
iex> s Map
@spec get(map(), key(), value()) :: value()
@spec put(map(), key(), value()) :: map()
# ...
@spec get(map(), key()) :: value()
@spec get_and_update!(map(), key(), (value() -> {get, value()})) :: {get, map()} | no_return() when get: term()
```

### `t`

The `t` helper tells us about Types available in a given module:

```elixir
iex> t Map
@type key() :: any()
@type value() :: any()
```

And now we know that Map defines key and value types in its implementation.
If we go and look at the source of `Map`:

```elixir
defmodule Map do
# ...
  @type key :: any
  @type value :: any
# ...
```

This is a simple example, stating that keys and values per the implementation can be any type, but it is useful to know.

By leveraging all these built-in niceties we can easily explore the code and learn more about how things work. IEx is very powerful and robust tool that empower developers. With these tools in our toolbox, exploring, and building can be even more fun!
