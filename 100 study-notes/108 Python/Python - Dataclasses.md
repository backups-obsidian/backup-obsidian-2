---
created: 2022-10-16 14:44
updated: 2022-10-27 20:32
---
---
**Links**: [[108 Python Index]]

---
## Dataclasses
- Dataclasses help us in writing *data oriented classes* like a class representing a vector.
	- They are very different from *behaviour oriented classes* that expose a number of methods.

- Example of a builtin dataclass

> [!question]- How does dataclasses help in representing data oriented classes?
> It adds convenience mechanisms like
> - **Being able to represent the object as a string easily**. Like `__str__` and `__repr__` methods are already implemented.
> - Compare the objects with other objects.
> - Add an easy initialisation mechanism to the data.

- Dataclasses by default automatically initialise a bunch of dunder methods for us in a class such as:
	- `__init__` The initialisation method for the class
	- `__repr__` How the class is represented with print() is called
	- `__str__` How the class is represented as a string (called with `__repr__`)
	- `__eq__` Used when equality operators are used (eg, `==`)
	- `__hash__` The hash for the class (called with `__eq__`)

> [!note]+ Dataclasses should be preferred over normal dictionaries and typedicts if you are want type hinting and are planning to pass dictionaries around.
> - Dataclasses can be used for creating Data Transfer Objects in Python. 
> - **DTOs are objects that are used to pass data between functions and are not entities or basic types**.

- If we forget to add `@dataclass` decorator over the class then we will end up with a bunch of class variables instead of instance variables.

### Using DataClasses
- Default values in non mutable objects
```python
from dataclasses import dataclass

@dataclass
class Test:
	a: str
	b: int = 10 # default value

obj1 = Test(a="abc")
```

- Default values in mutable objects
```python
from dataclasses import dataclass, field

def generate_id():
	return ""

@dataclass
class Person:
	name: str
	"""
	email_addrsses: list[str] = [] 
	Each instance of Person will have access to same version of list.
	"""
	email_addresses: list[str] = field(default_factory=list)

	"""
	Having randomly generated values as default ids
	init=False means we cannot set IDs while creating an instance
	"""
	id: str = field(init=False, default_factory=generate_id)

	"""
	Create values from other instance variables using post init
	"""
	search_string: str

	def __post_init__(self) -> None:
		self.search_string = self.name + self.id

obj1 = Test(a="abc")
obj2 = Test(a="abc", email_addresses=["xyz@gmail.com"])
```

- The above way of initialising the default value of b will lead to problems. Read more about [[Python - Everything is an object#Mutable default values | Mutable Default Values]]
- We pass functions to the `default_factory`
	- Here `list` is also a function, just like `generate_id`

- For *protected* variables we use `_` and for *private* variables we use `__` in front of the variables.
- If we don't want to print a particular field when we print the object then we can use `some_variable: str = field(repr=False)`
- We can freeze the dataclass using `@dataclass(frozen=True)`
	- *Frozen means once the dataclass is initialised it is read only*.
- After python3.10 we can use the following
	- `@dataclass(kw_only=True)` - If we only want to initialise the class by providing key word arguments.
	- `@dataclass(slots=True)` - Faster dataclasses

### Pydantic vs Dataclasses
- We also have pydantic which can be used as dataclass and comes with validators.
	- It does type checking at runtime.

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
- [This Is Why Python Data Classes Are Awesome - YouTube](https://www.youtube.com/watch?v=CvQ7e6yUtnw)