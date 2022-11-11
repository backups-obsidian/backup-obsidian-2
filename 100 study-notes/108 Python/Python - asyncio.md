---
created: 2022-09-16 16:24
updated: 2022-11-11 10:58
---
---
**Links**: [[108 Python Index]]

---
## Asyncio
- Asyncio is designed to allow you to **structure your code** so that when one piece of linear single-threaded code (called a "coroutine") is waiting for something to happen another can take over and use the CPU.
	- **It’s not about using multiple cores, it’s about using a single core more efficiently**

### [[Python - Asyncio - Coroutine vs Subroutine]]
### [[Python - Asyncio - Event Loop Tasks and coroutines| Event Loop, Tasks and Coroutines]]


---

- For using asyncio IO *we need to create coroutines*.
- When we call functions with `async` keyword in front of them it returns a coroutine object.
- To execute a coroutine we need to `await` its execution.
	- To use `await` you must be inside an `async` function.
- In python to execute async functions **we need to start the event loop**.
- **`await` is a blocking call**.
	- This means that the statements written below `await` can only be executed after the concurrent statement has completed.

- Example:
```python
import asyncio
from typing import Coroutine, Any
import time

urls = list(range(1, 10))


async def get_request(url: int) -> int:
    # send a request
    await asyncio.sleep(2)
    return url


async def make_requests():
    response_list: list[Coroutine[Any, Any, int]] = []
    for url in urls:
        response = get_request(url)
        response_list.append(response)

    results = await asyncio.gather(*response_list)
    print(results)


async def main():
    await make_requests()


asyncio.run(main())
```

- In the above example we first make all the requests, collect their future objects in an array and then await for them at the same time.
	- Instead of gather if I had used `await get_request(url)` inside the for loop then it would have been as if I am using a synchronous function since `await` is a blocking call.

---
- **Requests is a synchronous library**.

- Use asyncio over threading if you have a library that is non blocking in nature. For example if you want to use asyncio for making requests then you will have to use libraries like httpx or iohttp we cannot use requests since it is blocking in nature. If we want to use requests then we will have to use threading.
- Use asyncio with libraries that are non blocking in nature.

## References
- [Python Asyncio Part 1 – Basic Concepts and Patterns | cloudfit-public-docs (bbc.github.io)](https://bbc.github.io/cloudfit-public-docs/asyncio/asyncio-part-1.html) 
- [Diagnose slow Python code. (Feat. async/await) - YouTube](https://www.youtube.com/watch?v=m_a0fN48Alw): **Profiling and finding where asynio can be used**.

## ToDo
- [ ] Asyncio vs threading