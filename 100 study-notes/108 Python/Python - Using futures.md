---
created: 2022-11-09 20:08
updated: 2022-11-10 19:07
---
---
**Links**: [[108 Python Index]]

---
## Thread/Process Pooling
- A thread pool is a programming pattern for *automatically managing a pool of worker threads*.

- The *pool is responsible for a fixed number of threads*.
	-   It controls when the threads are created, such as just-in-time when they are needed.
	-   It also controls what threads should do when they are not being used, such as making them wait without consuming computational resources.
- **Each thread in the pool is called a worker or a worker thread**. 
	- Each worker is agnostic to the type of tasks that are executed.

- Advantages of a thread pool
	- Worker threads are *designed to be re-used once the task is completed* and *provide protection against the unexpected failure of the task, such as raising an exception*, without impacting the worker thread itself. This is unlike a single thread that is configured for the single execution of one specific task.

> [!important]- Thread pools can provide a **generic interface for executing ad hoc tasks** with a variable number of arguments, but do not require that we choose a thread to run the task, start the thread, or wait for the task to complete.
> It can be *significantly more efficient to use a thread pool* instead of manually starting, managing, and closing threads, especially with a large number of tasks.

- Similarly we use *Process pool for dealing with processes*.

## `concurrent.futures`
- `concurrent.futures` implements a simple, intuitive and a great API to deal with threads and processes. 
	- We know how to create processes and threads, but sometimes we require *something simpler*.
	- It hides most of the complexities of multi-threaded/process code and lets us focus on our thing, retrieve, process and apply CPU-hungry computations to data.

- From the official docs: The `concurrent.futures` module provides a high-level interface for asynchronously executing callables.

- The concept of *future* is the essence behind the simplicity of the `concurrent.futures`  
	- The _future_ is a proxy for a **result that does not exist yet but will exist in the future** it is something like a promise in JS.
- A *task is submitted to an executor*, and the executor **gives us back a future**. 
	- So we can think of it as a sort of receipt so that we can come back later and use it to get the result of our task. 
	- The executor will manage all the thread and process management for us.

- Anatomy of the `concurrent.futures`
	- ![[attachments/Pasted image 20221110151349.png]]

### Using `submit`
- Signature: `submit(fn, *args, **kwargs)`
- Schedules the callable, `fn`, to be executed as `fn(*args **kwargs)` and returns a `Future` object representing the execution of the callable.

- Simple example
```python
with ThreadPoolExecutor() as executor:
    future = executor.submit(pow, 323, 1235)
    print(future.result())
```

> [!note]- Rule for specifying `max_workers` in thread and process pool executor.
> - If you have CPU heavy tasks, use multiprocessing with `n_process = n_cores` and never more. Never!
> - We can get the cpu cores using `multiprocessing.cpu_count()`
> - If you have IO heavy tasks, use multithreading with `n_threads = m * n_cores` with `m` a number bigger than 1 that you **can tweak on your own**. 
> - Try many values and choose the one with the best speedup because there *isn’t a general rule*. 
> - For instance the default value of `m` in `ThreadPoolExecutor` is set to **5**

- `submit()` method *DOES NOT* block. 
- The `result()` method may block only if the task is not done by the time it is called.

- Another example: 
```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def get_request(url: str) -> str:
	# some processing
	return response

futures = {}
with ThreadPoolExecutor() as executor:
	for url in urls:
		futures[executor.submit(get_request, url)] = url  
		
    for fut in as_completed(futures):
		url = futures[fut]
		print(f"Url: {url}, result: {fut.result()}")
```

- Once all the tasks have been scheduled by submitting them, the method `concurrent.futures_as_completed()` is called, which yields the futures as they’re done – that is, as each task completes. 
- The *`fut.result()` method gives you the return value of `get_request(url)`*, or *throws an exception in case of failure*.
- The `executor.submit()` method *schedules the tasks asynchronously* and **doesn’t hold any contexts regarding the original tasks**. 
	- So *if you want to map the results with the original tasks, you need to track those yourself*. 
	- Like creating a dictionary in the above example.
	- One scenario where I think it is useful is when we want to cancel tasks. Tasks can be cancelled by calling `cancel()` method on the future object. Now If we have a logic in our program depending on which we want to cancel the execution of a task then we would need to know which future object to use and this can be done using a dictionary.

### Using map
- Signature: `map(func, *iterables, timeout=None, chunksize=1)`
- `map()` returns a generator; hence, the call *DOES NOT block*. 
- *However, popping elements from the generator may block in case the corresponding task is not done*.
	- We can pop the elements iterating through the generator.

- If `timeout` is not specified or `None`, there is no limit to the wait time.
	- The `timeout` parameter determines **how long a thread will spend before giving up on a single task** in the pipeline.
	- The returned iterator raises a `concurrent.futures.TimeoutError` if a single task takes more than `timeout` specified.
	- *This is useful since it lets us know that there is something wrong with a particular task (by raising an exception) instead of going on infinitely*.

- If a `func` *call raises an exception, then that exception will be raised when its value is retrieved from the iterator*.
- When using `ProcessPoolExecutor`, this method chops iterables into a number of chunks which it submits to the pool as separate tasks. 
	- The (approximate) size of these chunks can be specified by setting `chunksize` to a positive integer. 
	- For very *long iterables*, using a *large value for `chunksize` can significantly improve performance compared to the default size of 1*. 
	- **With ThreadPoolExecutor, `chunksize` has no effect**.

> [!important]- `map` returns the results in the same order in which they were sent.

- Example:
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

urls = list(range(1, 10))

def get_request(url: int) -> int:
    time.sleep(2)
    return url

futures = {}
with ThreadPoolExecutor(max_workers=13) as executor:
	results = executor.map(get_request, urls, timeout=60)

	for result in results:
		print(result)
```

- In case of `map` we don't need to call the `result` method.
- Example: There might be scenario where the function takes some fixed arguments, which is not an iterable.
	- We have to use [[Python - Miscellaneous#Partial Functions|Partial Functions]]

```python
from concurrent.futures import ThreadPoolExecutor, as_completed
from functools import partial
import time

urls = list(range(1, 10))

def get_request(url: int, sleep_time: int) -> int:
    time.sleep(sleep_time)
    return url

futures = {}
with ThreadPoolExecutor(max_workers=13) as executor:
	partial_get_request = partial(get_request, sleep_time=2)
	results = executor.map(partial_get_request, urls, timeout=60)

	for result in results:
		print(result)
```

### Exception handling between `map` and `submit`
- The `map()` method returns a generator which allows to iterate through the results once ready. 
- That means if *any error occurs inside `map`, it’s NOT possible to handle that and resume the generator after the exception occurs*.
	- **An unhandled exception terminates a generator’s useful life**.
- Example: 
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
from functools import partial
import time

urls = list(range(1, 10))

def get_request(url: int, sleep_time: int) -> int:
    time.sleep(sleep_time)
    if url == 5:
        raise Exception()
    return url

futures = {}
with ThreadPoolExecutor(max_workers=13) as executor:
    partial_get_request = partial(get_request, sleep_time=1)
    results = executor.map(partial_get_request, urls, timeout=60)

    try:
        for result in results:
            print(result)
    except Exception as e:
        pass

# not printing new lines to save line
# 1 2 3 4
```
- In the above example I have to wrap the for block in try catch otherwise it will error out.
- To get around this we can use `submit` instead of `map`.

```python
from concurrent.futures import ThreadPoolExecutor, Future
import time

urls = list(range(1, 10))

def get_request(url: int, sleep_time: int) -> int:
    time.sleep(sleep_time)
    if url == 5:
        raise Exception(f"Some exception in processing url {url}")
    return url

futures_list: list[Future[int]] = []
with ThreadPoolExecutor(max_workers=13) as executor:
    for url in urls:
        futures = executor.submit(get_request, url=url, sleep_time=2)
        futures_list.append(futures)

    for future in futures_list:
        try:
            result = future.result()
            print(result)
        except Exception as e:
            print(e)

# 1 2 3 4 
# Some exception in processing url 5
# 6 7 8 9
```
- We can access the exception of a future using `future.exception()`
- In case of `submit` we provide the `timeout` in `future.result()`, whereas in case of `map` we provide it in `map` itself.

> [!caution]+ Looking at the above scenario I think it is best to use `submit` over `map` because 
> - It is more flexible 
> - We can handle exceptions
> - Feels more natural
> - Easy to deal with functions that take arguments that are not iterables.

### Reusable pool executor
- Using an executor with a context manager ensures that terminating the pool is taken care of but it also means that it cannot be reused.
- If we often need continuous access to the pool and want to avoid the performance hit of pool creation and termination. 
	- In such cases, we can create an instance of the executor class, use it where we see fit, and terminate it manually using the `shutdown()` method

- Example:

```python
import time
from concurrent.futures import ThreadPoolExecutor as PoolExecutor
from functools import partial

def do_work(sleep_secs: float, i: int) -> str:
	time.sleep(sleep_secs)
	return f"foo-{i}"

def do_some_concurrent_work(executor: PoolExecutor) -> None:
	results_gen = executor.map(partial(do_work, 3.0), range(1, 10))
	print("some map results: ", list(results_gen))

def do_more_concurrent_work(executor: PoolExecutor) -> None:
	results_gen = executor.map(partial(do_work, 1.0), range(10, 20))
	print("more map results: ", list(results_gen))

# ----------------------------------------------------------------

if __name__ == "__main__":
	executor = PoolExecutor()
	do_some_concurrent_work(executor)
	do_more_concurrent_work(executor)
	executor.shutdown()
```

### Process pool
- **Both the thread pool (`ThreadPoolExecutor`) and the process pool (`ProcessPoolExecutor`) implement the same interface**. 
- They both inherit from the `Executor` class and implement the same three methods `submit()`, `map()`, `shutdown()`
- We can *switch from threads to processes with minimal code refactoring*.
> [!caution]- Just make sure you are using `if __name__ == "__main__":` everywhere.

```python
import time
from concurrent.futures import ProcessPoolExecutor as PoolExecutor
from functools import partial

def do_work(sleep_secs: float, i: int) -> str:
	time.sleep(sleep_secs)
	return f"foo-{i}"

def main() -> None:
	start_time = time.time()
	with PoolExecutor() as executor:
	results_gen = executor.map(partial(do_work, 3.0), range(1, 10))
	print("map results:", list(results_gen), "|", time.time() - start_time)

if __name__ == "__main__":
	main()
```

### Gotchas
- The simplicity provided by `concurrent.futures` has its fair share of constraints. 
- In this way, you *can apply concurrency only to the simplest of the tasks,* usually mapping a function to an iterable or running a few subroutines simultaneously. 
	- If your task at hand requires queuing, spawning multiple threads from multiple processes then you will still need to resort to the lower level `threading` and `multiprocessing` modules.

- Then there’re situations when you might be getting lower performance with concurrent code than its sequential counterpart. This could happen for multiple reasons.
	- Threads were used to perform CPU bound tasks
		- For CPU heavy tasks multithreading will make the code slower.
		- Imagine it like this: if you have 10 tasks and each takes 10 seconds, serial execution will take 100 seconds in total. However with multithreading, since only one thread is executed at any given time t, it will be like serial execution *PLUS the time spent to switch between the threads*.
	- Multiprocessing were used to perform I/O bound tasks
	- The tasks were too trivial to justify using either threads or multiple processes

## References
- [Python Concurrency — concurrent.futures | by Diego Barba | Towards Data Science](https://towardsdatascience.com/python-concurrency-concurrent-futures-15b56dc9a14d)
- [Effortless Concurrency with Python’s concurrent.futures | Red’s Digressions (rednafi.github.io)](https://rednafi.github.io/digressions/python/2020/04/21/python-concurrent-futures.html)
- [ThreadPoolExecutor in Python: The Complete Guide (superfastpython.com)](https://superfastpython.com/threadpoolexecutor-in-python/)