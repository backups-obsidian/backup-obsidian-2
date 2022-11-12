---
created: 2022-09-16 16:24
updated: 2022-11-11 19:47
---
---
**Links**: [[108 Python Index]]

---
## Asyncio
- Asyncio is designed to allow you to **structure your code** so that when one piece of linear single-threaded code (called a "coroutine") is waiting for something to happen another can take over and use the CPU.
	- **It’s not about using multiple cores, it’s about using a single core more efficiently**

### [[Python - Asyncio - Coroutine vs Subroutine]]
### [[Python - Asyncio - Event Loop Tasks and coroutines| Event Loop, Tasks and Coroutines]]
### [[Python - Asyncio - Awaitables Tasks and Futures]]

- In python to execute async functions **we need to start the event loop**. 
- Use asyncio over threading if you have a library that is non blocking in nature. 
	- For example if you want to use asyncio for making requests then you will have to use libraries like httpx or iohttp we cannot use requests since it is blocking in nature. 
	- If we want to use requests then we will have to use threading.

## References
- [Python Asyncio Part 1 – Basic Concepts and Patterns | cloudfit-public-docs (bbc.github.io)](https://bbc.github.io/cloudfit-public-docs/asyncio/asyncio-part-1.html) 
- [Diagnose slow Python code. (Feat. async/await) - YouTube](https://www.youtube.com/watch?v=m_a0fN48Alw): **Profiling and finding where asynio can be used**.
- [Asyncio: Understanding Async / Await in Python - YouTube](https://www.youtube.com/watch?v=bs9tlDFWWdQ)