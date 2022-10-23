---
created: 2022-10-16 14:44
updated: 2022-10-23 15:08
---
---
**Links**: [[108 Python Index]]

---
## Dataclasses
- For classes which are used for storing data

- Example of a builtin dataclass
```python
from dataclasses import dataclass

class Test:
	a: str
	b: int

obj1 = Test(a="abc", b=12)
```

- We also have pydantic which can be used as dataclass and comes with validators.
	- It does type checking at runtime.
- Dataclasses by default automatically initialise a bunch of dunder methods for us in a class such as:
	- `__init__` The initialisation method for the class
	- `__repr__` How the class is represented with print() is called
	- `__str__` How the class is represented as a string (called with `__repr__`)
	- `__eq__` Used when equality operators are used (eg, `==`)
	- `__hash__` The hash for the class (called with `__eq__`)

> [!note]+ Dataclasses should be preferred over normal dictionaries and typedicts if you are want type hinting and are planning to pass dictionaries around.
> - Dataclasses can be used for creating Data Transfer Objects in Python. 
> - **DTOs are objects that are used to pass data between functions and are not entities or basic types**.

### Pydantic vs Dataclasses
- Pydantic is an opinionated library built for parsing. 
- By default pydantic will try to convert the values in the types mentioned in the class. 

> [!note]+ **All the types are checked at runtime and type conversions take place at runtime**
> Checking/converting things at runtime makes a lot of sense when the data we are reading might not be valid or be of the right format.
> This only makes sense for the specific task of parsing.

- **Using pydantic is a huge waste of resources and time if parsing is not the goal**.
	- *If some data structure is internal to your application and your application is responsible for creating the data then it would be a huge waste of resources to do runtime checking*.

- It is better to use static type checking.
- Pydantic is not an alternative to dataclasses. 
	- They serve a very different purpose of parsing. Like parsing JSON from a web API.
- It is very good at what it does but it is not a general purpose thing.

## Dataclasses comparison chart
![[attachments/Pasted image 20221022152857.png]]

## References
- [Which Python @dataclass is best? Feat. Pydantic, NamedTuple, attrs... - YouTube](https://www.youtube.com/watch?v=vCLetdhswMg)
- [ ] https://www.youtube.com/watch?v=vBH6GRJ1REM 