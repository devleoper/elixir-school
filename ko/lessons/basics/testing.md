---
layout: page
title: 테스팅
category: basics
order: 12
lang: ko
---

소프트웨어를 개발하는 데 있어서 테스트는 아주 중요합니다. 이번 수업에서는 ExUnit을 사용해서 Elixir 코드를 테스트하는 방법과 테스팅하는 데 있어서 가장 효율적인 절차를 함께 살펴보도록 하겠습니다.

{% include toc.html %}

## ExUnit

ExUnit은 Elixir에 기본으로 내장된, 우리가 짠 코드를 철저하게 테스트할 수 있는 모든 도구를 포함한 테스트 프레일워크입니다. 시작하기 전에 먼저 이야기하자면, Elixir 스크립트로 테스트를 구현했기 때문에 `.exs` 파일 확장자를 사용하는 법을 알아둬야 합니다. 테스트를 실행하려면 `ExUnit.start()`로 ExUnit을 실행해야 하는데, 보통 `test/test_helper.exs` 파일에서 이 코드가 실행됩니다.

지난 수업에서 예제 프로젝트를 만들어 보았을 때, 굉장히 도움이 되는 mix가 간단한 테스트를 만들어줬습니다. `test/example_test.exs` 파일에서 확인해보도록 하지요:

```elixir
defmodule ExampleTest do
  use ExUnit.Case
  doctest Example

  test "the truth" do
    assert 1 + 1 == 2
  end
end
```

`mix test`를 입력하면 프로젝트에 있는 테스트를 실행할 수 있습니다. 지금 바로 테스트를 실행한다면 이런 결과를 볼 수 있겠네요:

```shell
Finished in 0.03 seconds (0.02s on load, 0.01s on tests)
1 tests, 0 failures
```

### assert

If you've written tests before then you're familiar with `assert`; in some frameworks `should` or `expect` fill the role of `assert`.

We use the `assert` macro to test that the expression is true.  In the event that it is not, an error will be raised and our tests will fail.  To test a failure let's change our sample and then run `mix test`:

```elixir
defmodule ExampleTest do
  use ExUnit.Case
  doctest Example

  test "the truth" do
    assert 1 + 1 == 3
  end
end
```

Now we should see a very different kind of output:

```shell
  1) test the truth (ExampleTest)
     test/example_test.exs:5
     Assertion with == failed
     code: 1 + 1 == 3
     lhs:  2
     rhs:  3
     stacktrace:
       test/example_test.exs:6

......

Finished in 0.03 seconds (0.02s on load, 0.01s on tests)
1 tests, 1 failures
```

ExUnit will tells us exactly where our failed assertions are, what the expected value was, and what the actual was.

### refute

`refute` is to `assert` as `unless` is to `if`.  Use `refute` when you want to ensure a statement is always false.

### assert_raise

Sometimes it may be necessary to assert that an error has been raised, we can do this with `assert_raise`.  We'll see an example of `assert_raise` in the next lesson on Plug.

## Test Setup

In some instances it may be necessary to perform setup before our tests.  To accomplish this we can use the `setup` and `setup_all` macros.  `setup` will be run before each test and `setup_all` once before the suite.  It is expected that they will return a tuple of `{:ok, state}`, the state will be available to our tests.

For the sake of example, we'll change our code to use `setup_all`:

```elixir
defmodule ExampleTest do
  use ExUnit.Case
  doctest Example

  setup_all do
    {:ok, number: 2}
  end

  test "the truth", state do
    assert 1 + 1 == state[:number]
  end
end
```

## Mocking

The simple answer to mocking in Elixir: don't.  You may instinctively reach for mocks but they are highly discouraged in the Elixir community and for good reason.  If you follow good design principles the resulting code will be easy to test as individual components.

Resist the urge.

