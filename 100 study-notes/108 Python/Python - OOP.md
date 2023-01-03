---
created: 2022-12-25 00:17
updated: 2022-12-25 00:22
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