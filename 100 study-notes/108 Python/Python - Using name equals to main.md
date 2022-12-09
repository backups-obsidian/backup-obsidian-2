---
created: 2022-12-08 13:23
updated: 2022-12-09 09:40
---
---
**Links**: [[108 Python Index]]

---
## Why use `__name__ == '__main__'`
> [!caution]- When you import a package in python it runs all the code in that file.

### Example 1
- Example for the above statement. In this example `main.py` and `some_package.py` are in the same folder.
```python
# main.py

from some_package import utils

def say_hello():
    print("hello")

if __name__ == "__main__":
    say_hello()
    utils()

# Output:
# Code from some_package
# hello
# Using some utils
```

```python
# some_package.py
def utils():
    print("Using some utils")

print("Code from some_package")
```

- We see that although we only wanted to import the `utils` function from `some_package.py` we ended up also executing the print statement.
- To prevent the behaviour we can wrap the part of `some_package.py` which we don't to execute inside `__name__ == '__main__'`
	- This means that run the following code only if it is run by the user and not when the filed is being imported as a package.
- **It also tells the user which files are executable and which are not**. 
	- Many editors (like pycharm) also use this as a signal.
		- ![[attachments/Pasted image 20221208161116.png]]
		- *We have a green arrow near `__name__ == '__main__'` but no green arrow on the right hand side*.
	- For example if you see `__name__ == '__main__'` then this means either that is the starting point of the program or that file can be executed as a script.
	- If you don't see this then it is safe to assume that the file is a part of a package and we are not meant to run it.

### Example 2
```python
# main.py

from some_package import utils

def say_hello():
    print("hello")

if __name__ == "__main__":
    say_hello()
    utils()

# Output:
# hello
# Using some utils
```

```python
# some_package.py
def utils():
    print("Using some utils")

if __name__ == "__main__":
	print("Code from some_package")
```

- **We should use `__name__ == '__main__'` since not every file is a script**. 
	- If we see `__name__ == '__main__'` then the file is supposed to be run as a script otherwise it is being imported as a package.

### Example 3
```python
# main.py
from some_package import utils

def say_hello():
    print("hello")

if __name__ == "__main__":
    say_hello()
    utils()
    print(f"Name of the package from main file: {__name__}")
```

```python
# some_package.py

def utils():
    print("Using some utils")
    print(f"Name of the package from the some_package file: {__name__}")


if __name__ == "__main__":
    print("Code from some_package")
    print(f"Name of the package from the some_package file: {__name__}")
```

- Output when we run `python main.py`
```python
# Output
# hello
# Using some utils
# Name of the package from the some_package file: some_package
# Name of the package from main file: __main__
```

- Output when we run `some_pacakge.py`
```python
# Output
# Code from some_package
# Name of the package from the some_package file: __main__
```

- **We see that the file that is run using `python` has the `__name__` set to `__main__` irrespective of its file name**.

## Why use `def main()`
- It helps preventing the pollution of global scope.

- Example:
```python
from some_package import utils

def say_hello():
    print("hello")

if __name__ == "__main__":
    i = 10
    print(globals())

# output
# {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x104f64f40>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': '/Users/sarthaknarayan/Desktop/testfolder/scratch.py', '__cached__': None, 'utils': <function utils at 0x105055c60>, 'say_hello': <function say_hello at 0x104ea3d90>, 'i': 10}
```

- Notice we have `i:10` in the end. 
	- Defining variables without a function polluted the global namespace.
	- We can avoid this by moving the code to `def main()` and then calling it from if `__name__ == "__main__"`

- So why is this trouble some:
- Example: Problems when you pollute the global namespace.

```python
def main():
    for j in range(10):
        # this works because of global variable population
        # this can lead to very hard to debug bugs
        print(i) # scenario of using i by mistake instead of j

if __name__ == "__main__":
    for i in range(10):
        ...
    main()

# Output
# 9 10 times
```

- Instead of getting an error I silently get the wrong value.
- This can also happen if you do a `import *` from a package. 
	- If that package has some global variables defined in it then they will also be imported.
	- **NEVER do `from package import *`**
	- Also always keep the code in packages in specific functions.

## References
- [You should put this in all your Python scripts | if __name__ == '__main__': ... - YouTube](https://www.youtube.com/watch?v=g_wlZ9IhbTs)
- [Always use: if __name__ == '__main__': ... in Python - YouTube](https://www.youtube.com/watch?v=_rtw5T0Bgo4)