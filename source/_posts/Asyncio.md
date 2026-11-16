---
title: "Asyncio"
date: 2023-12-30 16:27:58
categories:
  - "Python"
tags:
  - "Asyncio"
aging: true
aging_days: 30
---

## 协程学习

### 术语解释

> coroutine – 协程  
> 协程是子例程的更一般形式。 子例程可以在某一点进入并在另一点退出。 协程则可以在许多不同的点上进入、退出和恢复。 它们可通过 `async def` 语句来实现。

<!--more-->

> coroutine function – 协程函数  
> 返回一个 `coroutine` 对象的函数。协程函数可通过 async def 语句来定义，并可能包含 ``` await、``async for ``` 和 `async with` 关键字。

### 协程

## 通过 `async/await` 语法来声明协程是编写 `asyncio` 应用的推荐方式。 如下:

```python
async def func():
    print('hello')
    await asyncio.sleep(3)
    print('world')
 
# func() -> 直接调用协程不会使其被调度执行
asyncio.run(func())
```

要实际运行一个协程，asyncio 提供了以下几种机制: 1. `asyncio.run()` 函数用来运行最高层级的入口点 “main()” 函数 (参见上面的示例。) 2. 等待一个协程， 如下：

```python
import asyncio
import time
 
async def say_after(delay, what):
    await asyncio.sleep(delay)
    print(what)
 
async def main():
    print(f"started at {time.strftime('%X')}")
 
    await say_after(1, 'hello')
    await say_after(2, 'world')
 
    print(f"finished at {time.strftime('%X')}")
 
asyncio.run(main())
 
# -> print -> 执行了3s
started at 17:13:52
hello
world
finished at 17:13:55
```

3. `asyncio.create_task()`函数用来并发运行 ***作为 `asyncio` 任务的*** 多个协程。 如下：

```py
async def main():
    task1 = asyncio.create_task(
        say_after(1, 'hello'))
 
    task2 = asyncio.create_task(
        say_after(2, 'world'))
 
    print(f"started at {time.strftime('%X')}")
 
    # Wait until both tasks are completed (should take
    # around 2 seconds.)
    await task1
    await task2
 
    print(f"finished at {time.strftime('%X')}")
 
# -> print -> 执行了2s
started at 17:14:32
hello
world
finished at 17:14:34
```

4. `asyncio.TaskGroup` 类提供了 `create_task()` 的更现代化的替代。 使用此 API，之前的例子将变为:

```py
async def main():
    async with asyncio.TaskGroup() as tg:
        task1 = tg.create_task(say_after(1, 'hello'))
        task2 = tg.create_task(say_after(2, 'world'))
        print(f"started at {time.strftime('%X')}")
    print(f"finished at {time.strftime('%X')}")
asyncio.run(main())
```

### 可等待对象 如果一个对象可以在 `await` 语句中使用，那么它就是 `可等待` 对象。许多 asyncio API 都被设计为接受可等待对象。 可等待 对象有三种主要类型: **协程、任务 和 Future.** #### 协程 Python 协程属于 `可等待` 对象，因此可以在其他协程中被等待:

```py
async def nested():
    return 42
 
async def main():
    # 此时只调用 'nested()' 什么也不会发生。
    # 状态是 协程对象已创建但未等待，
    # 所以根本不会运行*。
    nested()
    print(await nested())  # will print "42".
 
asyncio.run(main())
```

## PS: 在本文档中 “协程” 可用来表示两个紧密关联的概念: 1. 协程函数: 定义形式为 async def 的函数; 2. 协程对象: 调用 协程函数 所返回的对象。

#### 任务

任务 被用来 **‘并行的’** 调度协程  
当一个协程通过 asyncio.create_task() 等函数被封装为一个 任务，该协程会被自动调度执行:

```py
import asyncio
 
async def nested():
    return 42
 
async def main():
    task = asyncio.create_task(nested())
    await task
 
asyncio.run(main())
```

#### Futures

没明白 摘取自官方文档

> Future 是一种特殊的 **低层级** 可等待对象，表示一个异步操作的 **最终结果** 。  
> 当一个 Future 对象 __被等待__，这意味着协程将保持等待直到该 Future 对象在其他地方操作完毕。  
> 在 asyncio 中需要 Future 对象以便允许通过 async/await 使用基于回调的代码。  
> 通常情况下 **没有必要** 在应用层级的代码中创建 Future 对象。  
> Future 对象有时会由库和某些 asyncio API 暴露给用户，用作可等待对象:

```py
async def main():
    await function_that_returns_a_future_object()
 
    # this is also valid:
    await asyncio.gather(
        function_that_returns_a_future_object(),
        some_python_coroutine()
    )
```

一个很好的返回对象的低层级函数的示例是 loop.run_in_executor()。

### 创建任务

`asyncio.TaskGroup.create_task(coro, *, name=None, context=None)`  
将 coro 协程 封装为一个 Task 并调度其执行。返回 Task 对象。

name 不为 None，它将使用 Task.set_name() 来设为任务的名称。

可选的 context 参数允许指定自定义的 contextvars.Context 供 coro 运行。 当未提供 context 时将创建当前上下文的副本。

该任务会在 get_running_loop() 返回的循环中执行，如果当前线程没有在运行的循环则会引发 RuntimeError。
