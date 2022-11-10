---
created: 2022-11-07 21:36
updated: 2022-11-09 20:34
---
---
**Links**: [[108 Python Index]]

---
## Multithreading
- **A normal thread will continue executing even if the main thread finishes**.
	- We can specify that we want the main thread to wait for other threads using the `.join()`
- There are 2 ways of creating threads in python
	- Class based 
	- Function based

###  Function based way for creating threads
```python
from threading import Thread
def func_4_thread(n_max: int = 1_000_000) -> None:
	n = 0
	while n < n_max:
		n += 1

# notice how we provide the arguments
my_thread = Thread(target=func_4_thread, args=(10_000_000,))
my_thread.start()
```

### Class based way for creating threads
- Inherit the `Thread` object and implement the `run` method

```python
from threading import Thread

class MyThread(Thread):
	def __init__(self, n_max=1_000_000) -> None:
		Thread.__init__(self)
		self.n_max = n_max

	def func_thread(self) -> None:
		n = 0
		while n < self.n_max:
			n += 1

	def run(self) -> None:
		self.func_thread()

my_thread = MyThread(n_max=1_000_000)
my_thread.start()
```


###  Joining the threads to the main thread
- Simple Example:
```python
# SuperFastPython.com

# example of executing a target task function in a separate thread

from time import sleep
from threading import Thread

# a simple task that blocks for a moment and prints a message
def task():
    sleep(1)
    print('This is coming from another thread')

thread = Thread(target=task)

# start the task in a new thread
thread.start()

print('Waiting for the new thread to finish...')
thread.join() # asking the main thread to wait for this thread to finish.
```

- This way of creating threads is useful for running one-off ad hoc tasks in a separate thread, although it becomes *cumbersome when you have many tasks to run*.
- Each thread that is created requires the application of resources (e.g. memory for the thread’s stack space). 
	- *The computational costs for setting up threads can become expensive if we are creating and destroying many threads over and over for ad hoc tasks*.
- Instead, we would prefer to **keep worker threads around for reuse** if we expect to run many ad hoc tasks throughout our program.
	- This can be *achieved using a thread pool*.

### Daemon threads
- At an OS level, _daemons_ are background processes that run without interaction with the user. 
- In the context of Python threads, daemons are simply background threads. 
- **The difference with normal threads is that the program will exit when there are only daemon threads running**. 
	- In other words, the program will wait for normal threads to finish (no cancellation); as soon as they are done, all running daemons will be terminated, and the program will exit.

- For a thread to be a daemon thread we specify `daemon = True`

### Return values from threads
- There is no way of returning from a thread so we use queues to store the results.
	- Thread object does not have a way of returning objects from the target.

- Example:
```python
import time
from threading import Thread
from queue import Queue

def func_4_thread(q_out: Queue) -> None:
	print("thread doing work...")
	time.sleep(5)
	func_result = func()
	q_out.put(func_result)

func_result_queue: Queue = Queue(maxsize=0)
thread = Thread(target=func_4_thread, args=(func_result_queue,))
thread.start()

func_result = func_result_queue.get()
print(func_result, "from queue")
```

### Using locks in threading
> [!question]- When to use a lock?
> Are the objects I’m using and the operation I’m performing thread-safe? If then then we should use a `lock()`.

- Example: 
```python
import time
from threading import Thread, Lock

my_cache = {str(i): i for i in range(100)}
lck = Lock()

def check_cache() -> None:
	while True:
		with lck:
			for key, value in my_cache.items():
				# do important stuff with cache items
				pass

def add_to_cache() -> None:
	while True:
		with lck:
			current_time = time.time_ns()
			my_cache[str(current_time)] = current_time
			time.sleep(0.1)

check_cache_thread = Thread(target=check_cache)
check_cache_thread.start()

add_to_cache_thread = Thread(target=add_to_cache)
add_to_cache_thread.start()
```

- What happens if we don't apply a lock here?
	- There is chance then when `check_cache()` acquires the GIL it is iterating through the cache.
	- While it is halfway done iterating `add_to_cache()` acquires the GIL and modifies the size of the cache.
	- Now when `check_cache` occupies the GIL again it finds that the dictionary size has changed and will give a `RuntimeError: dictionary changed size during iteration.`

- A lock ensures `add_to_cache` doesn't acquire the GIL while `check_cache` is working on it.

---
> [!tip]+ **Threads are a simple way to achieve concurrency**. 
> We *don’t need event loops*, funky function definitions, or spawning additional processes and communicating with them.

## References
- [Python Concurrency — Threading and the GIL | by Diego Barba | Towards Data Science](https://towardsdatascience.com/python-concurrency-threading-and-the-gil-db940596e325)