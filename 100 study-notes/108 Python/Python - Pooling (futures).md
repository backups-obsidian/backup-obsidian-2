---
created: 2022-11-09 20:08
updated: 2022-11-09 22:11
---
---
**Links**: [[108 Python Index]]

---
## Thread pooling
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

- The concept of _future_ is the essence behind the simplicity of the `concurrent.futures`  
- The _future_ is a proxy for a **result that does not exist yet but will exist in the future**. 
- A *task is submitted to an executor*, and the executor **gives us back a future**. 
	- So we can think of it as a sort of receipt so that we can come back later and use it to get the result of our task. 
	- The executor will manage all the thread and process management for us.

### Thread pool example
```python
import time
from concurrent.futures import ThreadPoolExecutor as PoolExecutor

def do_work(sleep_secs: float = 10.0) -> str:
	time.sleep(sleep_secs)
	return "foo"

def wait_for_future() -> None:
	print("-------- Wait for future --------")
	start_time = time.time()
	with PoolExecutor() as executor:
		future = executor.submit(do_work, sleep_secs=5.0)
		print("future created", "|", time.time() - start_time)
		print("waiting for future...", "|", time.time() - start_time)
		result = future.result()
		print("future result:", result, "|", time.time() - start_time)

wait_for_future()
```

- `submit()` method *DOES NOT* block. 
- The `result()` method may block only if the task is not done by the time it is called.

### Using map
- `map()` returns a generator; hence, the call *DOES NOT block*. 
- *However, popping elements from the generator may block in case the corresponding task is not done*.
- Example:

```python
import time
from concurrent.futures import ThreadPoolExecutor as PoolExecutor
from functools import partial

def do_work(sleep_secs: float, i: int) -> str:
	time.sleep(sleep_secs)
	return f"foo-{i}"

def main() -> None:
	start_time = time.time()
	with PoolExecutor() as executor:
		results_gen = executor.map(partial(do_work, 10.0), range(1, 10))
		print("map generator created", "|", time.time() - start_time)
		print("waiting for map results...", "|", time.time() - start_time)
		print("map results:", list(results_gen), "|", time.time() - start_time)

main()
```

- The signature of the function passed to `map()` is expected to have a single argument, the iterable. 
	- If our function does not have the same signature, as this is the case, we can use a `partial` function from the `functools` module

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

## References
- [Python Concurrency — concurrent.futures | by Diego Barba | Towards Data Science](https://towardsdatascience.com/python-concurrency-concurrent-futures-15b56dc9a14d)