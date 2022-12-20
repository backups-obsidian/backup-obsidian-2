---
created: 2022-12-18 18:37
updated: 2022-12-18 19:44
---
---
**Links**: [[108 Python Index]] 

---
## Protocols
- Can not adapt type hints of imported code.
- ABCs (Abstract base classes) are classes that we can inherit from but *they CANNOT be instantiated*.
	- They are used to define the interface of what subclasses should look like.
- The problem with this approach was that any external classes that didn't inherit abstract base classes were not of that type.
- This problem was solved by protocol. 
	- If a class implements all the methods of a protocol class then it is automatically of that type (for static type checking and not at runtime).

## References
- [Protocols in Python: Why You Need Them - presented by Rogier van der Geer - YouTube](https://www.youtube.com/watch?v=Lddegb2ToNY) 