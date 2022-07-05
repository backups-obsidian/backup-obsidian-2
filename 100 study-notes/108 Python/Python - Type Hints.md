---
created: 2022-07-04 20:22
updated: 2022-07-05 15:51
---
---
**Links**: [[108 Python Index]]

---
## Type Hints
- Type Hints were first introduces in Python 3.5
- Python’s type hints provide you with optional static typing to leverage the best of both static and dynamic typing.
- With type hints you don't have to write doc strings
- Until a popular package incorporates type hints, we can use Type Hinting Package Stubs (THPS).

> [!note]- Type hints *do not affect the Python interpreter*. Type Hints are *optional*.
> You can put them in or leave them out of your code with no effect. As far as you are concerned, the Python interpreter ignores type hints.

### Type checking tools
- To check the syntax for type hints, we need to use a *static type checker tool*.
- Some tools are:
	- `mypy` (Dropbox)
	- `pytype` (Google)
	- `pyre` (Facebook)
	- `pyright` (Microsoft)
	- `PyCharm` (JetBrains)

-  Installing and using the tool: `pip install mypy`, `mypy app.py`

### Basics
- Syntax of type hints: `<variable name> : <type name>`
- The basic types are `int`, `float`, `str`, `bool`, `bytes`, `list`, `tuple`, `dict`, `set`, `frozenset`, `None`.
	- Some of them like `dict`, `list` and etc may have become a basic type after python 3.9.

```python
def say_hi(name: str) -> str: 
	return f'Hi {name}'
```

### Type Hinting Functions
- Default values
```python
def f(num1: int, my_float: float = 3.5) -> float:
    return num1 + my_float

```

- If a function doesn’t explicitly returns a value, you can use None to type hint the return value. 
```python
def log(message: str) -> None:
    print(message)
```

- Generators
```python
# A generator function that yields ints is secretly just a function that
# returns an iterator of ints, so that's how we annotate it
def g(n: int) -> Iterator[int]:
    i = 0
    while i < n:
        yield i
        i += 1
```

- Function annotation can be split over multiple lines
```python
def send_email(address: Union[str, list[str]],
               sender: str,
               cc: Optional[list[str]],
               bcc: Optional[list[str]],
               subject='',
               body: Optional[list[str]] = None
               ) -> bool:
```


### Fixing the type of the variable
```python
name: str = 'Hello'
name = 100

""" 
error: Incompatible types in assignment (expression has type "int", variable has type "str")

Adding a type to a variable is unnecessary because static type checkers typically can infer the type based on the value assigned to the variable.

name = 'Hello' 
name = 100

This will give the same error as above
But the first approach is more descriptive and better IMO
"""
```

### Multiple Types
- To set type hints for multiple types, you can use `Union` from the `typing` module.
	- `from typing import Union`

```python
from typing import Union 

def add(x: Union[int, float], y: Union[int, float]) -> Union[int, float]: 
	return x + y
```

- Starting from *Python 3.9*, you can use the `X | Y` syntax to create a union type.
```python
def add(x: int | float, y: int | float) -> int | float:
    return x + y
```

### Type Aliases
```python
from typing import Union

number = Union[int, float]

def add(x: number, y: number) -> number:
    return x + y
```

```python
Genome = list[int] # this is a type alias
Population: list[Genome] # this is a variable
```

### Type Hinting Dictionaries and Lists
- In newer versions of python we can use 
```python
myList: list[str] = ["hello", "there"]
myDict: dict[str, int] = {"1": 32, "3": 45}
```

- In older versions of python you will have to use the `typing` module
```python
from typing import Dict, List

dict_of_users: Dict[int,str] = {
    1: "Jerome",
    2: "Lewis"
}

list_of_users: List[str] = [
    "Jerome", "Lewis"
]
```

### Type Hinting in Classes
```python
class User:
    def __init__(self, name:str, address:"Address"):
        self.name = name
        self.address = address

class Address:
    def __init__(self, owner:User, address_line:str):        
        self.owner = owner
        self.address_line = address_line
```

- We can define the data types of the attributes 
```python
class Test:
    name: str

    def __init__(self, name: str) -> None:
        self.name = name
```

- In case of recursive data structures we have to enclose the type in quotes if they are of the same class.
```python
from typing import Optional

class BinaryTree:
    leftNode: Optional["BinaryTree"] = None
	# leftNode: "BinaryTree" | None = None 
	# the above would have also worked
    rightNode: Optional["BinaryTree"] = None
```

- In the above example `Optional is needed because`
	- ![[attachments/Pasted image 20220705131900.png]]

### Generics
- Generics are useful when we don't know the the exact type but we know it will be the same
```python
from typing import TypeVar

T = TypeVar("T")

def myFunction(lst: list[T], index: int) -> T:
    return lst[index]

myFunction([1, 2, 3], 2)
```

- We could have passed it a list of strings or list of actually anything and it would have worked fine.

### `Any` type
```python
from typing import Any

def myFunction(a: Any) -> None:
    print(a)

myFunction(45)
```

### `Optional` type
- We use optional for types that could be `None`
	- ![[attachments/Pasted image 20220705120727.png]]

```python
from typing import Any, Optional

# this means our function can return either string or None
def myFunction(a: Any) -> Optional[str]:
    print(a)

myFunction("test")
```

- We can also use optional for variables: `x : Optional[int] = 34`
- It is badly named. It is not the optional type in functions. It is just a shortcut for Union between `None` and the type in between the brackets. In the above example it is `int`.

### `Sequence` type
- It is a list of strings, integers etc.
- Anything that can be iterated over.

```python
from typing import Sequence

# this is a sequence of unknown values
# we can also be more specific Sequence[int]
def test(lst: Sequence) -> None:
    for i in lst:
        print(i)

test([1, 2, 34])
test("hello world") # since string is a sequence of characters
```

- Dictionaries and sets are not considered as sequence.

### `Callable` type
```python
from typing import Callable

def firstFunc(a: int, b: str) -> bool:
    print(a, b)
    return True

def secondFunc(func: Callable[[int, str], bool]) -> None:
    func(4, "te")

secondFunc(firstFunc)
```

## References
- [Type hints cheat sheet (Python 3) - mypy 0.961 documentation](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)