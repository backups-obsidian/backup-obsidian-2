---
created: 2022-09-16 16:24
updated: 2022-12-18 10:24
---
---
**Links**: [[108 Python Index]]

---
## Everything is an object
- Every preexisting thing in python is an object.
	- Strings, numbers, functions and even classes.
- We can check this using the `type` functions.
	- ![[attachments/Pasted image 20221024190210.png]]
	- We see that all the entities are instantiated from a class which means all of them are objects.

> [!important]+ We can list out all the attributes and methods of an object by using the `dir()` method.
> Another usage of `dir()` -> [[Python - Modules and Packages#Using `dir()`|Listing objects in namespaces]]

- In Python, **every object has an id for identity**. 
	- The id of an object is always *unique and constant* for this object during its lifetime.
	- The `id()` function returns the object's memory address.

```python
number1 = 5
print(id(number1)) # 9785152

number2 = 10
print(id(number2)) # 9784992

number2 = number1
print(id(number2)) # 9785152
# number2 and number1 have the same id. Python does this for memory optimization
# we are working with the same object.
```

> [!question]+ What do you mean when you say almost everything is an object in Python?
> It means that almost everything that you use:
> - It is an instance of a class
> - It has attributes
>
> I say "almost" because this doesn't hold for keywords (such as `for`, `if`, `def` etc).
> **Everything is an instance of a class**

## Mutable and immutable objects
- Mutable types: `list`, `dict`, `sets`
	- A dictionary is mutable but its keys are not
- Immutable types: `int`, `float`, `str`, `bool`, `tuple`
- Full list of mutable and immutable objects
	- ![[attachments/Pasted image 20221027140235.png]]
- In python variables are pointers to the data but they behave differently for mutable and immutable types.
	- To simplify the concept think of them as pointers for mutable types.

```python
# mutable types
a = []
b = a
print(id(a) == id(b)) # true

# unmutable types
a = 3
b = a
print(id(a) == id(b)) # true
a = a + 3
print(id(a) == id(b)) # true
# python does some behind the scenes magic for unmutable data types
```

### How are passed arguments to functions and what does that imply for mutable and immutable objects?
- Unlike languages like C, which allow arguments to be passed by value or reference to a function, **Python uses a mechanism called "Call-by-Object"**
- When an *immutable object* (such as an integer, a string, a tuple… ) is passed as an argument to a function, it is **passed as a value, not as a reference**. 
	- And it makes sense if we know that as immutable objects these cannot be modified. 
	- So no matter what happens in the function, the values are not going to be modified directly.
- On the other hand, when the **arguments are mutable objects, they are passed as a reference to the objects and can be modified by the function**. 
	- So, if we pass to a function a list, the list’s values can be modified by the function itself. 
	- *If, we do not want to modify the original list that we pass as an argument we must create a copy of the list within the function*. 
		- That way, we will have a new memory address and a new list as a local variable.

## Mutable default values
- **Do not use mutable default arguments in Python**
-  *The value of a default argument for a function is **evaluated** only once, when the function is declared*.
	- In short *default arguments are evaluated once* the function is created.
- We can look at the attribute (`__defaults__`) of our function which holds the default values.

```python
def confused(a, e=[]):
    e.append(a)
    return e

print(confused.__defaults__) # ([],)
print(confused(10)) # [10]
print(confused.__defaults__) # ([10],)
print(confused(20)) # [10, 20]
print(confused.__defaults__) # ([10, 20],)
# it is using the same list when the function was defined.
```

- *If you think about it from the angle of `__defaults__` attribute it all starts to make sense*.
	- For mutable objects variables are like pointers.

> [!note]- Default values are **only evaluated once but set each time (with `__defaults__`) a function is called**. 
> - In case of mutable objects too the function was evaluated only once and `__defaults__` was set to `[]` 
> - But each time the function is called the default value (`__defaults__`) of the function is changing since it is pointer to `[]` and the default value is being set to that.

- Correct way of doing it

```python
def confused(a, e=None):
	if e is None:
		e = []
    e.append(a)
    return e

print(confused.__defaults__) # (None,)
print(confused(10)) # [10]
print(confused.__defaults__) # (None,)
print(confused(20)) # [10, 20]
print(confused.__defaults__) # (None,)
```

- **Class example**

```python
class Test:
	def __init__(self, a=[]) -> None:
		self.a = a

obj1 = Test()
obj2 = Test()
print(id(obj1) == id(obj2)) # False
print(id(obj1.a) == id(obj2.a)) # True
obj1.a.append("abc")
print(obj2.a) # ['abc']
print(Test.__init__.__defaults__) # (['abc'],)
```

- The `__init__` method of the class is only evaluated once but at the time of evaluation `__init__.__defaults__` is set to point to `[]`.
	- Now when we create objects out of the class we call `__init__` method to set each object's variable `a` to point to the same location (`[]`)
	- Hence we `id(obj1.a) == id(obj2.a)`

- Another Example
	- ![[attachments/Pasted image 20221026093057.png]]

## References
- [For Python, Everything is an Object | by Diana Henao | Python in Plain English](https://python.plainenglish.io/for-python-everything-is-an-object-1b2c45cbbea)
- [Python Tutorial: Clarifying the Issues with Mutable Default Arguments - YouTube](https://www.youtube.com/watch?v=_JGmemuINww)