---
created: 2022-12-20 19:13
updated: 2022-12-20 23:13
---
---
**Links**: [[108 Python Index]]

---
## Descriptors
- Descriptors are *Python objects* that implement a method of the **descriptor protocol**, which gives you the *ability to create objects that have special behaviour when they’re **accessed as attributes** of other objects*. 

```python
__get__(self, obj, type=None) -> object
__set__(self, obj, value) -> None
__delete__(self, obj) -> None
__set_name__(self, owner, name)
```

- If your descriptor implements just `.__get__()`, then it’s said to be a **non-data descriptor**. 
- If it implements `.__set__()` or `.__delete__()`, then it’s said to be a **data descriptor**.

> [!note]- The most important methods of the descriptor protocol are `.__get__()` and `.__set__()`.

```python
class Verbose_attribute():
    def __get__(self, obj, type=None) -> object:
        print("accessing the attribute to get the value")
        return 42
    def __set__(self, obj, value) -> None:
        print("accessing the attribute to set the value")
        raise AttributeError("Cannot change the value")

class Foo():
    attribute1 = Verbose_attribute()

my_foo_object = Foo()
x = my_foo_object.attribute1
print(x)
```

- `Verbose_attribute()` implements the descriptor protocol. 
	- Once it’s instantiated as an attribute of `Foo`, it can be considered a descriptor.
- As a descriptor, it has *binding behaviour when it’s accessed using dot notation*. 
	- In this case, the descriptor logs a message on the console every time it’s accessed to get or set a value
		- When it’s accessed to `.__get__()` the value, it always returns the value 42.
		- When it’s accessed to `.__set__()` a specific value, it raises an `AttributeError` exception, which is the *recommended way to implement read-only descriptors*.

- We may think that the previous example’s approach is a bit of overkill, we could achieve the same result by using properties. 
	- While this is true, you may be surprised to know that **properties in Python are just descriptors**.

### How attributes are accessed within the lookup chain

> [!important]- In Python, every object has a built-in `__dict__` attribute.
> This is a dictionary that *contains all the attributes* defined in the object itself.

```python
class Vehicle():
    can_fly = False
    number_of_weels = 0

class Car(Vehicle):
    number_of_weels = 4

    def __init__(self, color):
        self.color = color

my_car = Car("red")
print(my_car.__dict__)
print(type(my_car).__dict__) # print(Car.__dict__)

# Output:
# {'color': 'red'}
# {'__module__': '__main__', 'number_of_weels': 4, '__init__': <function Car.__init__ at 0x10fdeaea0>, '__doc__': None}
```

- In Python, **everything is an object**. 
	- *A class is actually an object as well, so it will also have a `__dict__` attribute that contains all the attributes and methods of the class*.

#### Lookup chain rule with example

```python
# lookup.py
class Vehicle(object):
    can_fly = False
    number_of_weels = 0

class Car(Vehicle):
    number_of_weels = 4

    def __init__(self, color):
        self.color = color

my_car = Car("red")

print(my_car.color)
print(my_car.number_of_weels)
print(my_car.can_fly)

# Output:
# red
# 4
# False
```

- In the above the example when we are accessing the attribute `color` of the instance `my_car`, we are actually accessing a single value of the `__dict__` attribute of the object `my_car`. 
- When you access the attribute `number_of_wheels` of the object `my_car`, you’re really accessing a single value of the `__dict__` attribute of the class `Car`. 
- Finally, when you access the `can_fly` attribute, you’re actually accessing it by using the `__dict__` attribute of the `Vehicle` class.

- **Lookup chain** rules:
	- First, you’ll get the result returned from the *`__get__` method of the data descriptor* named after the attribute you’re looking for.
	- If that fails, then you’ll get the value of your *object’s `__dict__`* for the key named after the attribute you’re looking for.
	- If that fails, then you’ll get the result returned from the *`__get__` method of the non-data descriptor* named after the attribute you’re looking for.
	- If that fails, then you’ll get the value of your *object type’s (class) `__dict__`* for the key named after the attribute you’re looking for.
	- If that fails, then you’ll get the value of your *object parent type’s (parent class) `__dict__`* for the key named after the attribute you’re looking for.
	- If everything else has failed, then you’ll get an `AttributeError` exception.

- We can see why it’s important to know if a descriptor is a **data** descriptor or a **non-data** descriptor.

## TLDR
- It is important to note that **descriptors are assigned to a class, not to the instance of a class**.
	- Cannot be used inside the init block of the class.

> [!caution]- There is a slight problem in Descriptors and which is that when you create a new instance or a second instance of the class, the previous instance value gets overridden. *The reason is that Descriptors are linked to class and not the instance*.

- Example
```python
class BoundedNumber:
    def __init__(self, min_val, max_val):
        self.min_val = min_val
        self.max_val = max_val

    def __set_name__(self, owner, name):
        self.name = name  # name of the variable, useful when printing errors

    def __set__(self, instance, value):
        if self.min_val > value or value > self.max_val:
            msg = "{} takes values between {} and {}".format(
                self.name,
                self.min_val,
                self.max_val,
            )
            raise ValueError(msg)
        instance.__dict__[self.name] = value

    def __get__(self, instance, owner=None):
        return instance.__dict__[self.name]


class Person:
    age = BoundedNumber(1, 120)
    weight = BoundedNumber(1, 250)
    height = BoundedNumber(1, 230)

    def __init__(self, name: str, age: int, weight: int, height: int):
        self.name = name
        self.age = age
        self.weight = weight
        self.height = height


person = Person("Sarthak", age=34, weight=67, height=169)
person.age = 900
```


## References
- [Python Descriptors Tutorial | DataCamp](https://www.datacamp.com/tutorial/python-descriptors)
- [Python Descriptors: An Introduction – Real Python](https://realpython.com/python-descriptors/)