---
created: 2022-11-14 12:22
updated: 2023-01-27 15:50
---
---
**Links**: [[108 Python Index]]

---
## Function Decorators
> [!note]+ By definition, a decorator is a function that takes another function and extends the behaviour of the latter function without explicitly modifying it.
> Decorators don't change anything inside the function they are decorating. They change and manipulate the return value of the function.

- In Python, functions are first-class objects. 
	- This means that **functions can be passed around and used as arguments**, just like any other object (string, int, float, list, and so on).
- **A decorator is a higher order function**.
	- It takes an entity (a standalone function, a class function, or even a whole class) and returns a function or a class reference.

```python
def say_hello(name):
    return f"Hello {name}"

def greet_bob(greeter_func):
    return greeter_func("Bob")

greet_bob(say_hello) # Hello Bob
```

- In python **we can define functions within other functions**.

### Simple decorators
```python
from datetime import datetime

def not_during_the_night(func):
    def wrapper():
        if 7 <= datetime.now().hour < 22:
            func()
        else:
            pass  # Hush, the neighbors are asleep
    return wrapper

def say_whee():
    print("Whee!")

say_whee = not_during_the_night(say_whee)
say_whee()

@not_during_the_night
say_whee()
```

- `@` is just syntactic sugar.
- The inner function (`wrapper` in the above example) can be named anything.

### Passing argument to functions
```python
def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        func(*args, **kwargs)
    return wrapper_do_twice

@do_twice
def greet(name):
    print(f"Hello {name}")
```

### Returning values from decorators
```python
def do_once(func):
    def wrapper_do_once(*args, **kwargs):
        return func(*args, **kwargs) # if I don't return from here then the print(return_value) will be None
    return wrapper_do_twice

@do_once
def greet(name):
    print(f"Hello {name}")
	return "test"

return_value = greet("function") # Hello function
print(return_value) # test
```

### Decorated functions identity
```python
def some_decorator(func):
	def wrapper():
		print("inside the some_decorator")
		func()
	return wrapper

@some_decorator
def learn_decorators():
	print("Inside learn decorators function")

learn_decorators()
print(learn_decorators.__name__)

# inside the some_decorator 
# Inside learn decorators function 
# The name of the function is wrapper
```

- The function has forgotten its identity.
- We can rectify this using `functools`

```python
import functools

def some_decorator(func):
	@functools.wraps(func)
	def wrapper():
		print("inside the some_decorator")
		func()
	return wrapper

@some_decorator
def learn_decorators():
	print("Inside learn decorators function")

learn_decorators()
print("The name of the function is ", learn_decorators.__name__)

# inside the some_decorator 
# Inside learn decorators function 
# The name of the function is learn_decorators
```

### Stacked decorators
```python
def deco_1(original_func):
    def wrapper(*args, **kwargs):
        print(args, kwargs)
        print("Inside deco_1")
        original_func(*args, **kwargs)

    return wrapper

def deco_2(original_func):
    def wrapper(*args, **kwargs):
        print(args, kwargs)
        print("Inside deco_2")
        original_func(*args, **kwargs)

    return wrapper

@deco_1
@deco_2
def some_function(a: int, b: int) -> None:
    print(a * b)


some_function(4, 5)
```

- These can be useful in performing some kind of validations on the input arguments there by separating the validation logic from the function.
	- Like checking if the input integer parameter is greater than 20 or not.

```python
def check_int(original_func):
    def wrapper(*args, **kwargs):
        for argument in args:
            if not isinstance(argument, int):
                raise TypeError(f"{argument} is not of type Int")

        original_func(*args, **kwargs)

    return wrapper

def greater_than_20(original_func):
    def wrapper(*args, **kwargs):
        for argument in args:
            if argument < 20:
                raise ValueError(f"{argument} is not > 20")
        original_func(*args, **kwargs)

    return wrapper

@check_int
@greater_than_20
def some_function(a: int, b: int) -> None:
    print(a * b)

some_function(12, 40)
```

### Using decorators for class methods
```python
def check_int(original_func):
    def wrapper(self, *args, **kwargs):
        for argument in args:
            if not isinstance(argument, int):
                raise TypeError(f"{argument} is not of type Int")

        original_func(self, *args, **kwargs)

    return wrapper

def greater_than_20(original_func):
    def wrapper(self, *args, **kwargs): # notice the self argument
        for argument in args:
            if argument < 20:
                raise ValueError(f"{argument} is not > 20")
        original_func(self, *args, **kwargs) # notice the self argument

    return wrapper

class SimpleClass:
    @check_int
    @greater_than_20
    def __init__(self, a: int, b: int):
        self.a = a
        self.b = b

SimpleClass(10, 43)
# this will give a value error
```

### Passing arguments to decorators
- Nested functions (3)

## Class Based Decorators

### Decorators without arguments
```python
class say_hi:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        print("hello there")
        result = self.func(*args, **kwargs)
        return result

@say_hi
def some_function(a: int, b: int) -> int:
    return a * b

print(some_function(a=10, b=20))

# hello there
# 200
```

### Decorators with arguments
- **Always use this. Even if you are not passing arguments**. 
	- Using the above example causes typing issues.
	- You can use the decorator as `@some_decorator()`
```python
import functools

class say_hi:
    def __init__(
        self,
        *decorator_args,
        **decorator_kwargs,
    ):
        self.decorator_args = decorator_args
        self.decorator_kwargs = decorator_kwargs

    def __call__(self, original_func):
        @functools.wraps(original_func)
        def inner_function(*original_args, **original_kwargs):
            print("hello there")
            print(self.decorator_args)
            print(self.decorator_kwargs)
            result = original_func(*original_args, **original_kwargs)
            return result

        return inner_function

@say_hi(age=16, value=34)
def some_function(a: int, b: int) -> int:
    return a * b

print(some_function(a=10, b=20))
print(some_function.__name__)

# hello there
# ()
# {'age': 16, 'value': 34}
# 200
# some_function
```

> [!caution]- The arguments shift down one level.

- *Specific arguments to the decorator*

```python
import functools

class say_hi:
	# usig age and value specifically in the decorator
    def __init__(self, age: int, value: int) -> None:
        self.age = age
        self.value = value

    def __call__(self, original_func):
        @functools.wraps(original_func)
        def inner_function(*original_args, **original_kwargs):
            print("hello there")
            print(self.age)
            print(self.value)
            result = original_func(*original_args, **original_kwargs)
            return result

        return inner_function


@say_hi(age=10, value=10)
def some_function(a: int, b: int) -> int:
    return a * b


print(some_function(a=10, b=20))
print(some_function.__name__)

# hello there
# 10
# 10
# 200
# some_function
```

- *Using above example for simple decorators where I don't need arguments*.

```python
import functools


class say_hi:
    def __init__(self) -> None:
        ...

    def __call__(self, original_func):
        @functools.wraps(original_func)
        def inner_function(*original_args, **original_kwargs):
            print("hello there")
            result = original_func(*original_args, **original_kwargs)
            return result

        return inner_function


@say_hi()
def some_function(a: int, b: int) -> int:
    return a * b


print(some_function(a=10, b=20))
```

## Summary
![[attachments/Pasted image 20221121162725.png]]

## Use Cases
- Caching
- Timing

## References
- [Mastering Decorators in Python 3. Ever wondered how a python decoration… | by Arian Seyedi | Level Up Coding (gitconnected.com)](https://levelup.gitconnected.com/mastering-decorators-in-python-3-588cb34fff5e)
	- Great for understanding and writing decorators as classes
- [Primer on Python Decorators – Real Python](https://realpython.com/primer-on-python-decorators/)
- [decorator typing (PEP 612) (intermediate - advanced) anthony explains #386 - YouTube](https://www.youtube.com/watch?v=fwZoxWyMGM8) 
	- Decorator typing