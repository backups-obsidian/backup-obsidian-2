---
created: 2022-11-11 10:56
updated: 2022-11-11 10:57
---
---
**Links**:
- [[108 Python Index]]
- [[Python - Asyncio]]

---
## Event loops, tasks and coroutines
- Asyncio starts an event loop.
	- *The event loop contains within it a list of objects called Tasks*. Each Task maintains a single stack, and its own execution pointer as well.
	- There are *2 types of task in the event loop*: *active task* and *paused task*.
	- ![[attachments/Pasted image 20221111104511.png]]
- **At any one time the event loop can ONLY have ONE Task** actually executing (the processor can still only do one thing at a time, after all), whilst the *other tasks in the loop are all paused*. 
- The *currently executing task will continue to execute exactly as if it were executing a function in a normal (synchronous) Python program*, right up until it gets to a point where it would have to wait for something to happen before it can continue.
- Then, **instead of waiting, the code in the Task yields control**. 
- This means that it asks the event loop to pause the Task it is running in, and wake it up again at a future point once the thing it needs to wait for has happened.
- The *event loop can then select one of its other sleeping tasks to wake up and become the executing task instead*. 
	- Or if none of them are able to awaken (because they’re all waiting for things to happen) then it can wait.
- This way the CPU’s time can be shared between different tasks, all of which are executing code capable of yielding like this when they would otherwise wait.

> [!caution]+ An **event loop CANNOT forcibly interrupt a coroutine that is currently executing**. 
> - A coroutine that is executing will continue executing **until it (coroutine) yields control**. 
> - The *event loop serves to select which coroutine to schedule next*, and keeps track of which coroutines are blocked and unable to execute until some IO has completed, *but it only does these things when no coroutine is currently executing*.
> - *This is different from multithreading in python since the interpreter can interrupt executing threads which is why we need locks in threading and not in asyncio*.

- This approach works well for IO-bound code, where long pauses are expected to wait for something else (often another computer) to respond to a request.