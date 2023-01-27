---
created: 2022-12-25 00:17
updated: 2023-01-23 09:32
---
---
**Links**: [[108 Python Index]]

---
## OOP
- In Object-oriented programming, we use instance methods and class methods. 
- Inside a Class, we can define the following three types of methods.
	- *Instance method*: 
		- Used to **access or modify the object state**. 
		- If we use instance variables inside a method, such methods are called instance methods. 
		- It must have a `self` parameter to refer to the current object.
	- *Class method*: 
		- Used to **access or modify the class state**. 
		- In method implementation, if we use only class variables, then such type of methods we should declare as a class method. 
		- The class method has a `cls` parameter which refers to the class.
	- *Static method*: 
		- It is a general utility method that *performs a task in isolation*. 
		- Inside this method, we don’t use instance or class variable because this static method **doesn’t take any parameters like `self` and `cls`**.

> [!note]- Python manages separate dictionary-like objects to track the members/attributes of any instance and class. 
> So, when we access the variable from the instance, Python checks if the variable exists in the instance’s dictionary-like object and if *it does not exist then it checks the class’s dict-object*.

### What is the use of class methods?
- Class methods are used when we are **dealing with factory methods**. 
	- Factory methods are those methods that **return a class object for different use cases**. 
	- `__init__` is an example of a factory method.
- By default any method in a class is an instance method and to define a class method we need to use `@classmethod` decorator.

- The class method can be called using `ClassName.method_name()` as well as by using an object of the class.
	- First way is the most common way of doing it.
	
> [!important]- In general it is used for having different constructors.
> `__init__` is the default constructor but we can use class methods to have different constructors for the same class which return the object of a class.

```python
from datetime import date

class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def calculate_age(cls, name, birth_year):
        # calculate age an set it as a age
        # return new object
        return cls(name, date.today().year - birth_year)

    def show(self):
        print(self.name + "'s age is: " + str(self.age))

jessa = Student('Jessa', 20)
jessa.show()

# create new object using the factory method
joy = Student.calculate_age("Joy", 1995)
joy.show()
```

### PEP-0526
- According to PEP 526, **class variables that are defined directly on the class would be considered instance variables if they were annotated with a type**.
- The purpose of PEP 526 is to introduce a syntax for variable annotations in Python, which allows developers to explicitly indicate the type of a variable. 
- When using this syntax, *a variable defined directly on a class would be **considered an instance variable if it is annotated with a type**, rather than a class variable*.

```python
class MyClass:
    class_variable: str = "I am a class variable"
    def __init__(self, value):
        self.instance_variable = value
```

- This variable will be considered an instance variable according to the PEP 526, even though it is defined directly on the class and is shared among all instances of the class.
- The above style is consistent with dataclasses.

```python
class Foo:
    x: Union[int, str]

    def __init__(self, an_int: int) -> None:
        self.x = an_int

# This is same as the one below

class Foo:
    def __init__(self, an_int: int) -> None:
        self.x: Union[int, str] = an_int
```

```python
class Foo:
    x: int
```

- We are not actually annotating a _class variable_. 
	- At this point, **x has no value, so doesn't actually exist as a variable**.
- The only thing you're creating is an _annotation_, which is just **pure metadata and distinct from the variable itself**.
- But if we do:

```python
class Foo:
    x: int = 3
```

- We are *now creating both a class variable and an annotation*. 