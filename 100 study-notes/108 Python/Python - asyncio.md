---
created: 2022-09-16 16:24
updated: 2022-11-09 22:11
---
---
**Links**: [[108 Python Index]]

---
- **Requests is a synchronous library**.

- Use asyncio over threading if you have a library that is non blocking in nature. For example if you want to use asyncio for making requests then you will have to use libraries like httpx or iohttp we cannot use requests since it is blocking in nature. If we want to use requests then we will have to use threading.
- Use asyncio with libraries that are non blocking in nature.

## References
- [ ] [async io in python - YouTube](https://www.youtube.com/results?search_query=async+io+in+python)
- [ ] [Diagnose slow Python code. (Feat. async/await) - YouTube](https://www.youtube.com/watch?v=m_a0fN48Alw)
- [ ] Asyncio vs threading

## ToDo
- [ ] In the multi threading example don't forget to change the number of worker processes and notice the difference in speed.
	- Its speed should match the asyncio speed.