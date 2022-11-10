---
created: 2022-11-09 19:20
updated: 2022-11-09 21:46
---
---
**Links**: [[108 Python Index]]

---
## Multiprocessing
- GIL doesn't interfere with processes they are simultaneous.

### Simple Example
```python
import time
from multiprocessing import Process

def do_stuff(sleep_secs: int) -> None:
	print("doing stuff...")
	time.sleep(sleep_secs)
	print("stuff done")

# ----------------------------------------------------------------
if __name__ == "__main__":
	proc = Process(target=do_stuff, args=(10,))
	proc.start()
	proc.join()
```

> [!caution]- The main difference is that we need to start the new process by using the idiom: `if __name__ == ‘__main__’:` otherwise we get a *RuntimeError*

### Multiple processes
```python
import time
from multiprocessing import Process

def do_stuff(name: str, sleep_secs: int) -> None:
	print(name, "doing stuff...")
	time.sleep(sleep_secs)
	print(name, "stuff done")

def create_procs(n_procs: int) -> list[Process]:
	return [Process(target=do_stuff, args=(str(i), 10)) for i in range(n_procs)]

def start_procs(procs: list[Process]) -> None:
	for proc in procs:
		proc.start()

# ----------------------------------------------------------------

if __name__ == "__main__":
	procs = create_procs(5)
	start_procs(procs)
```

### Child Processes and Daemons
- **Any process can start a new process**
	- It can have "child" processes, except when the "parent" process is a _daemon_ process.

> [!important]- All processes we may create are children of the main Python process. 

- Daemon processes have two differences from normal processes:
	- They *cannot have child processes*.
	- The *program will not wait for them to finish before exit*.

```python
import time
from multiprocessing import Process

def foo():
	# start daemon child
	# a process starting a process
	child_proc = Process(target=foo_child, daemon=True)
	child_proc.start()
	print("parent sleeping for 5 seconds...")
	time.sleep(5)
	print("parent done")

def foo_child():
	print("daemon child sleeping for 10 seconds...")
	time.sleep(10)
	print("child done")

if __name__ == "__main__":
	proc = Process(target=foo)
	proc.start()

# parent sleeping for 5 seconds…  
# daemon child sleeping for 10 seconds...  
# parent done
```

### Contexts
- *Multiprocessing* contexts allow us to select how a child process starts, i.e., *what it inherits from the parent process*. There are 2 main choices:
	-   *spawn*: 
		- Starts an *entirely NEW Python process*. 
		- The new process will not inherit unnecessary objects from the parent. 
		- In particular, it does not copy thread locks. 
		- **This method is the default for macOS and Windows.**
	-   *fork*: 
		- It is a **COPY** of the parent process. 
		- While it does not copy threads, it does copy thread locks. 
		- **It is the default in Unix**. 
		- This method is *considered thread-unsafe and, in particular, may cause crashes in subprocesses on macOS*.

- **Spawn is safer but slower as compared to fork**.
- Nothing will happen if 2 processes created using spawning try to modify the global variable.
	- This is because the spawn child process is a fresh Python process, *the global variable behaves as different variables for all practical purposes*.

```python
from multiprocessing import Process
def func_4_proc(number: int, max_iter: int = 10) -> None:
	print("proc", "number init", number)
	for i in range(max_iter):
		number += 1
	print("proc", "number done", number)

if __name__ == "__main__":
	number = 0

	proc_1 = Process(target=func_4_proc, args=(number, 100))
	proc_1.start()

	proc_2 = Process(target=func_4_proc, args=(number, 100))
	proc_2.start()

	proc_1.join()
	proc_2.join()
	print("main done, number", number)
```

### Locks, Queues and Pipes
- *Because of GIL when using threads, we can avoid using locks, and usually, we would be ok*.
- Locks are a must when using shared variables between processes that do write operations.
- *We cannot use the same queue that we used in multithreading to communicate between processes* we use a multiprocess safe queue.
	- `from multiprocessing import Queue`
	- If we use `from queue import Queue` the program will get deadlocked.

> [!note]- Queues are great for one way communication but for 2 way communication we use pipes.

## References
- [Python Concurrency — Multiprocessing | by Diego Barba | Towards Data Science](https://towardsdatascience.com/python-concurrency-multiprocessing-327c02544a5a)