---
created: 2022-11-11 17:02
updated: 2022-11-11 19:46
---
---
**Links**: 
- [[108 Python Index]]
- [[Python - Asyncio]]

---
## Awaitables, Tasks and Futures
- `async def` is used to *declare an asynchronous coroutine function* in the same way that `def` is used to define a normal synchronous function.
```python
async def example_coroutine_function(a, b):
    # Asynchronous code goes here
    ...

def example_function(a, b):
    # Synchronous code goes here
    ...
```
- Asynchronous Python code can **only** be included inside a suitable context that allows it that means inside a coroutine function defined using `async def`.

### Difference between `def` and `async def`
- The Python `def` keyword creates a callable object with a name, when the object is called the code block of the function is run. 
	- Eg: `r = example_function(1, 2, 3)` causes the function code to be run immediately as a subroutine call, and its return value to be assigned to `r`
- The Python `async def` keyword creates a callable object with a name, **when the object is called the code block of the function is NOT run**.
	- Eg: `r = example_coroutine_function(1, 2, 3)` this does not cause the function code block to be run. 
	- Instead an object of class `Coroutine` is created, and is assigned to `r`.
	- To make the code block actually run we would need `await` or `asyncio.gather`

> [!note]- If we are using the `typing` library then the declaration of coroutine functions can be a little confusing at times.
> - For `async def example_coroutine_function(A,B) -> C` Typing defines `example_coroutine_function` as a callable that takes two parameters of types A and B and returns an object of type `Coroutine[Any, Any, C]`. 
> - *The two `Any` type parameters in the above definition are related to the way that the event loop works*.

### `Await` and awaitables
- `await` is a new keyword which can only be used in `async def` functions and not anywhere outside of that.
- Eg use of `await`: `r = await some_async_func()`
	- The return value of the `await` statement is the value returned by the code block.
- **An object that can be used with `await` is known as an awaitable object**.
	- A coroutine object is `awaitable`
- **Awaiting a Coroutine object is like calling a function**.
	- Only when a coroutine object is awaited a task for it is created in the event loop and its execution starts. Just getting the Coroutine object by calling the async function doesn't put it as a task.
- **Await is a blocking call**	
- There are *three types of objects* that are awaitable:
	- **A Coroutine object**. 
		- **When awaited it will execute the code-block of the coroutine in the *current* Task**.
		- The `await` statement will return the value returned by the code block.
	- **Any object of class `asyncio.Future`** which when awaited causes the current Task to be paused until a specific condition occurs.
	- An object which implements the magic method `__await__`, in which case what happens when it is awaited is defined by that method.

#### How NOT to use `await`
- Example: Since `await` is a blocking call, how NOT to use `await`
```python
import asyncio
from asyncio import Task
import time

urls = list(range(1, 6))

async def get_request(url: int) -> int:
    # send a request and assuming the the process of sending it is awaitable
    await asyncio.sleep(1)
    return url

async def make_requests():
    response_list = []
    for url in urls:
        # the process of creating a task starts the function
        response = get_request(url)
        response_list.append(response)

    for i in range(len(response_list)):
        result = await response_list[i]
        print(result)

async def main():
    start_time = time.perf_counter()
    await make_requests()
    end_time = time.perf_counter()
    print(end_time - start_time)

asyncio.run(main())

# OUTPUT
# 1 2 3 4 5
# 5.00723379199917
```
- The above example is same as running synchronous code

#### Only when a coroutine object is awaited a task for it is created in the event loop and its execution starts.
```python
import asyncio
from typing import Coroutine, Any
import time

urls = list(range(1, 6))


async def get_request(url: int) -> int:
    # send a request and assuming the the process of sending it is awaitable
    await asyncio.sleep(1)
    return url

async def make_requests():
    response_list: list[Coroutine[Any, Any, int]] = []
    for url in urls:
        response = get_request(
            url
        )  # this hasn't started function execution (i.e. created a task)
        response_list.append(response)

    print(f"Number of tasks: {len(asyncio.all_tasks())}")
    # yielding control back to the event loop but since there is only one task it doesn't matter
    await asyncio.sleep(2)
    results = await asyncio.gather(*response_list)
    print(results)

async def main():
    start_time = time.perf_counter()
    await make_requests()
    end_time = time.perf_counter()
    print(end_time - start_time)

asyncio.run(main())

# OUTPUT
# Number of tasks: 1
# [1, 2, 3, 4, 5]
# 3.0037487500085263
```

- In the above example if the task was created at the time of `get_request()` was called then the overall execution time should have been only 2s.
	- This can be achieved using `create_task()`

### Tasks
- **Tasks are used to schedule a coroutine to run in the event loop**.
- *The method `create_task` takes a coroutine object as a parameter and returns a `Task` object, which inherits from `asyncio.Future`.* 
- *The call creates the task inside the event loop for the current thread, and starts the task executing at the beginning of the coroutine’s code-block*. 
- The returned future will be marked as `done()` only when the task has finished execution. 
- The return value of the coroutine’s code block is the `result()` which will be stored in the future object when it is finished.

> [!question]- Difference between Coroutine and a Task
> When we call `create_task()` it creates a `Task` which is a wrapper around a `future`.
> When we call an async function it creates a Coroutine.
> The distinction between a Coroutine and a Task/Future is that Coroutine’s code will not be executed until it is awaited.

#### Using `create_task()`
- Example: using `create_task()`
```python
import asyncio
from asyncio import Task

urls = list(range(1, 6))

async def get_request(url: int) -> int:
    # send a request and assuming the the process of sending it is awaitable
    await asyncio.sleep(1)
    return url

async def make_requests():
    response_list: list[Task[int]] = []
    for url in urls:
        # the process of creating a task starts the function
        response = asyncio.create_task(get_request(url))
        response_list.append(response)

    print(f"Number of tasks: {len(asyncio.all_tasks())}")
    # yielding control back to the event loop
    await asyncio.sleep(2)
    results = await asyncio.gather(*response_list)
    print(results)

async def main():
    start_time = time.perf_counter()
    await make_requests()
    end_time = time.perf_counter()
    print(end_time - start_time)

asyncio.run(main())

# OUTPUT
# Number of tasks: 6
# [1, 2, 3, 4, 5]
# 2.001688958000159
```
- *`create_task()` immediately creates a task in the event loop and executes it when control is given to it by the event loop unlike `await` which creates a task when it is called*.

### Using synchronous code in asyncio
- One of the most important points to get across is that the currently executing Task (in the event loop, [[Python - Asyncio - Event Loop Tasks and coroutines|Reference Diagram]]) *cannot* be paused by any means other than awaiting a future.
	- And that is something which can only happen inside asynchronous code. 
	- So any `await` statement _might_ cause your current task to pause, but is not guaranteed to. 
	- **`await` can be thought of as a checkpoint where it's safe for asyncio to go to another coroutine**.
- Conversely **any statement which is not an `await` statement  *cannot* cause the current Task of the event loop to be paused**.
	> [!note]- **This is a very important point since it proves that libraries that are synchronous (eg: Requests) provide no value when used with asyncio**.
	- If we want to use synchronous libraries then we will have to use threading for concurrent programming.

> [!question]+ Difference between threading and asyncio
> - In case of threads the operating system decides when to context switch to another thread. Context can switch at any point of time.
> - **In case of asyncio the tasks themselves decide when to hand over the control using the `await` keyword**.

#### No point using synchronous libraries with asyncio
```python
import requests
import asyncio
import time

async def counter():
    now = time.time()
    print("Started counter")
    for i in range(0, 10):
        last = now
        await asyncio.sleep(0.001)
        now = time.time()
        print(f"{i}: Was asleep for {now - last}s")

async def main():
    t = asyncio.create_task(counter())

	# switch the context to the counter task.
    await asyncio.sleep(0)

    print("Sending HTTP request")
    r = requests.get('http://example.com')
    print(f"Got HTTP response with status {r.status_code}")

    await t

asyncio.run(main())

# OUTPUT
# Started counter
# Sending HTTP request
# Got HTTP response with status 200
# 0: Was asleep for 0.019963502883911133s
# 1: Was asleep for 0.0012884140014648438s
# 2: Was asleep for 0.0012254714965820312s
# 3: Was asleep for 0.0011649131774902344s
# 4: Was asleep for 0.0011239051818847656s
# 5: Was asleep for 0.0012202262878417969s
# 6: Was asleep for 0.0012269020080566406s
# 7: Was asleep for 0.001184701919555664s
# 8: Was asleep for 0.0011556148529052734s
# 9: Was asleep for 0.00115203857421875s
```

- As you can see the counter has *paused* during the whole time it takes to make the HTTP request. 
	- This is because the call `requests.get` is an ordinary synchronous IO call, and does not return until the http request has been completed. 
	- *Since it is not asynchronous code the event loop can do nothing to interrupt it to let other tasks run, and so it "**blocks the event loop**" for as long as it runs*.

## References
- [Python Asyncio Part 2 – Awaitables, Tasks, and Futures | cloudfit-public-docs (bbc.github.io)](https://bbc.github.io/cloudfit-public-docs/asyncio/asyncio-part-2)