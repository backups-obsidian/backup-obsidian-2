---
created: 2022-09-16 16:24
updated: 2022-11-11 20:27
---
---
**Links**: [[108 Python Index]]

---
## Multiprocessing vs Multithreading 
> [!important]- Difference between concurrency and parallelism
> *Concurrency* is about **DEALING with a lot of things at same time** (gives the **illusion of simultaneity**) or handling concurrent events essentially hiding latency. 
> On the contrary, *parallelism* is about **DOING a lot of things at the same time**. 

- In Python **Multiprocessing is parallelism. Multithreading is concurrency**
	- Multiprocessing is for increasing speed. Multithreading is for hiding latency.

- **Asyncio** (*Asynchronous programming*): *Single thread* switching between tasks
	- ![[attachments/Pasted image 20221111201249.png]]
- **Multithreading** (*Concurrent programming*): *Multiple threads* but only one can run at a time due to GIL.
	- ![[attachments/Pasted image 20221111201311.png]]
- **Multiprocessing** (*Parallel programming*): *Multiple processes* running at the same time.
	- ![[attachments/Pasted image 20221111201328.png]]
	- In this example each thread belongs to a different process.

> [!note]- Because of python's GIL, multithreading and asyncio are doing the same thing only difference is that **asyncio uses only 1 thread whereas multithreading is using multiple threads**.

### What is a process?
- A **process is an instance of a program**.
- A process has **its own memory** for code and data.
- A process has **atleast one thread**.
- This *thread has its own register and stack*.
- This thread has access to data of the process.
- A process can spawn another thread which is known as multithreading.
	- ![[attachments/Pasted image 20221107171831.png]]
	- Each thread will have its own register and stack but it will share process's memory space.
- If another instance of the same program is launched then it creates another process.
	- ![[attachments/Pasted image 20221107172212.png]]
	- It has its own set of memory which is not shared with other processes.
	- In order to share data it must do it with queues or pipes.
	- Threads can also use pipes and queues to share data but they have some overhead.

### Multithreading
> [!caution]- Python is multithreaded but *NOT simultaneously multithreaded* i.e. threads in python are **concurrent but NOT parallel**. 
> - This means each thread is subdivided in time but **NO two threads from the SAME PROCESS can ever execute at the same time**.
> - On the other hand processes can execute simultaneously.
> ![[attachments/Pasted image 20221107172448.png]]
> Both threads and processes can jump around on the physical CPU cores.

- In order **for the thread to execute it must acquire the GIL** (Global interpreter lock) within the process.
	- ![[attachments/Pasted image 20221110190821.png]]
	- Only one thread in a process can acquire a GIL at a time.
	- *By ensuring that only a single thread can execute at one time thread safety is guaranteed in python*. 
	- Thread safety means there can be no deadlocks.
	- *This is the reason why we do not have simultaneous multithreading in python*.

- This means intense CPU tasks cannot be parallelised using multithreading in python.
- But I/O limited tasks are not affected.
	- *I/O operations are **blocking** which means once the I/O request is made the **thread must wait** for the return value*.
	- Some examples are reading from a file, send http requests, DNS queries, etc.
- Example of an I/O bound task:
	- Here thread 0 in each process is an I/O bound task
	- ![[attachments/Pasted image 20221107173348.png]]
	- **CPython implementation will consider switching threads (releasing GIL) every 15ms or when an I/O operation is encountered**. This allows other threads to execute.
	
- Difference between threads and processes
	- ![[attachments/Pasted image 20221107173803.png]]

> [!question]- Why do we need locks even if we have GIL?
> - Threading allows process data to be accessed by multiple threads.
> - Locks ensure that if any thread is interrupted halfway through changing or accessing some data then another thread cannot read it or modify it during interruption.
> - Hence we protect the data using `Lock()`

### Comparison between performance
- With SMT (Simultaneous multithreading) enabled if you have a 8 core machine then you can have 16 threads. 
	- So if you running a program using 4 threads then running it on 8 threads will give you a performance improvement of 2x.
	- But if you increase the threads from 8 to 16 you will only have a performance improvement of around 25%.
	- Makes sense since we only have 8 cores.

- An *activity plot* of multithreading (8 threads) vs multiprocessing (8 processes) for a **CPU intensive task**.
	- ![[attachments/Pasted image 20221107182207.png]]
	- If you notice you can see that at any given time we are only executing one thread.
- *Actual work done*:
	- ![[attachments/Pasted image 20221107182432.png]]
	- **Adding more threads did not result in anymore work being done**.

- The GIL lock only lives inside a process hence we can use different cores simultaneously using multiprocessing. 
	- In multiple processes you will have multiple locks and those can run in parallel.

> [!note]- Use multiprocessing for CPU bound tasks and use multithreading for I/O bound tasks in Python
> Program can do other things while waiting for the I/O.
> Multithreading is generally used for fetching data in the background or refreshing the UI.

> [!danger]+ Incorrectly using either multiprocessing or multithreading can negatively impact the performance of the program.
> - There are a lot of setup costs associated with multiprocessing. 
> - We can incur *performance hit* while doing multiprocessing if we have a *very simple function* that doesn't actually do a lot and we are sending a lot of data to the simple function.
> - The overhead in sending data to the function dwarfs any performance gains achieved by multiprocessing.
> - The *functions that benefits* from multiprocessing are the ones that *perform a lot of operations on relatively small amount of data*.

- Use processes when tasks are CPU intensive and completely independent from each other.

### Conclusions
- Multi-threaded does not strictly mean single-core the OS may switch the python process between physical and virtual CPU cores!
- *Multiprocessing is the standard python way to increase processing power if needed*.
- Most numerical libraries (numpy, scipy, tensorflow) are, *simultaneously multi-threaded* behind the scenes
	- *These modules will not benefit from multiprocessing as they use all available cores*.
- `queue()` is thread and process safe but it has more overhead as compared to `pipes()`

## References
- [threading vs multiprocessing in python - YouTube](https://www.youtube.com/watch?v=AZnGRKFUU0c) - **MUST WATCH**
- [Unlocking your CPU cores in Python (multiprocessing) - YouTube](https://www.youtube.com/watch?v=X7vBbelRXn0)
- [Making multiple HTTP requests using Python (synchronous, multiprocessing, multithreading, asyncio) - YouTube](https://www.youtube.com/watch?v=R4Oz8JUuM4s) - speed comparisons