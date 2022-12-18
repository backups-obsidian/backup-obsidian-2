---
created: 2022-12-17 11:52
updated: 2022-12-18 17:35
---
---
**Links**: [[108 Python Index]]

---
## Modules & Packages
- Python **modules** and Python **packages**, two mechanisms that facilitate **modular programming**.
- *Functions*, *modules* and *packages* are all constructs in Python that promote code modularisation*.

>[!important]+ `.py` files which are not executed directly are known as modules and a collection of modules is known as a package.
> - **Packages** allow for a hierarchical structuring of the module namespace using **dot notation**. 
> - In the same way that **modules** *help avoid collisions between global variable names*, **packages** help avoid collisions between module names.
> - This means *we can have modules with the same name in different packages*.

- Three different ways to define modules in python:
	- A module can be written in Python itself.
	- A module can be written in C and loaded dynamically at run-time, like the `re` (regular expression) module.
	- A built-in module is intrinsically contained in the interpreter, like the `itertools` module.
- A module's contents are accessed the same way in all three cases: with the `import` statement.

### Importing modules
- When the interpreter executes the import statement, it searches for the module in a list of directories which can be printed using `pprint.pprint(sys.path)`
	-  The exact contents of `sys.path` are installation-dependent.
	- This list of directories can be modified by setting `PYTHONPATH` variable from outside of python or the list of directories can be modified by doing `sys.path.append("some-path")` from inside of python.
- Once a module has been imported, you can determine the location where it was found with the module’s `__file__` attribute:
- Example:
	- ![[attachments/Pasted image 20221217164524.png]]
	- The directory portion of `__file__` should be one of the directories in `sys.path`.

#### Importing a module doesn't make its contents directly accessible to the caller
- Each module has its own **private symbol table**, which serves as the global symbol table for all objects defined _in the module_. 
	- Thus, a module creates a separate **namespace**.

> [!important]+ The statement `import <module_name>` only places `<module_name>` in the caller's symbol table. 
> The _objects_ that are defined in the module _remain in the module’s private symbol table_.

- From the caller, objects in the module are only accessible when prefixed with `<module_name>` via **dot notation**.

- Examples: 

```python
# some_package.py

def utils():
    print("Using some utils")
    print(f"Name of the package from the some_package file: {__name__}")

some = 10
print("Executed some_package.py")
```

```python
import some_package
from pprint import pprint

pprint(globals())

# Output:

# Executed some_package.py
# {'__annotations__': {},
# '__builtins__': <module 'builtins' (built-in)>,
# '__cached__': None,
# '__doc__': None,
# '__file__': '/Users/sarthaknarayan/Desktop/testfolder/scratch.py',
# '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x102b1cf40>,
# '__name__': '__main__',
# '__package__': None,
# '__spec__': None,
# 'pprint': <function pprint at 0x102c0dd80>,
# 'some_package': <module 'some_package' from '/Users/sarthaknarayan/Desktop/testfolder/some_package.py'>}
```
- Note the last line `'some_package': <module 'some_package' from '/Users/sarthaknarayan/Desktop/testfolder/some_package.py'>`
	- This shows that only `some_package` was placed in the caller's symbol table but the objects (`utils` and `some`) defined in `some_package` remained in its private symbol table.
	- To access them we would need to use the dot notation.

```python
from some_package import some
from pprint import pprint

pprint(globals())

# Output:
# Executed some_package.py
# {'__annotations__': {},
#  '__builtins__': <module 'builtins' (built-in)>,
#  '__cached__': None,
#  '__doc__': None,
#  '__file__': '/Users/sarthaknarayan/Desktop/testfolder/scratch.py',
#  '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x104ab8f40>,
#  '__name__': '__main__',
#  '__package__': None,
#  '__spec__': None,
#  'pprint': <function pprint at 0x104b91f30>,
#  'some': 10}
```
- Notice the last line: `'some:' 10`
	- As compared to above example we have `some` instead of having the whole of `some_package` private symbol table.

> [!caution]- Any kind of `from` import places the object names of the module being imported directly into the caller’s symbol table, **any objects that already exist with the same name will be overwritten**

- Namespace pollution example:
```python
from some_package import *
from pprint import pprint

pprint(globals())

# Output:
# Executed some_package.py
# {'__annotations__': {},
#  '__builtins__': <module 'builtins' (built-in)>,
#  '__cached__': None,
#  '__doc__': None,
#  '__file__': '/Users/sarthaknarayan/Desktop/testfolder/scratch.py',
#  '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x104d28f40>,
#  '__name__': '__main__',
#  '__package__': None,
#  '__spec__': None,
#  'pprint': <function pprint at 0x104e19d80>,
#  'some': 10,
#  'utils': <function utils at 0x104e19c60>}
```
- Notice that both `some` and `utils` function got imported from the `some_package` module.
	- So if you had a function named `sum` in `some_package` and used `from some_package import *` for importing then this would have *overwritten the inbuilt `sum` function*.
	- Since python looks for an object according to the [[Python - Namespaces#Namespace scoping rules | LEGB]] rule.
	- The exception to the above rule is that if the symbols of module being imported start with `_` (underscore) then they won't be imported using `from some_module import *`

### Importing modules with alternate names
- Example:
```python
from some_package import utils as huhu, some as tutu
from pprint import pprint

pprint(globals())

# Output:
# Executed some_package.py
# {'__annotations__': {},
#  '__builtins__': <module 'builtins' (built-in)>,
#  '__cached__': None,
#  '__doc__': None,
#  '__file__': '/Users/sarthaknarayan/Desktop/testfolder/scratch.py',
#  '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x100c5cf40>,
#  '__name__': '__main__',
#  '__package__': None,
#  '__spec__': None,
#  'huhu': <function utils at 0x100d300d0>,
#  'pprint': <function pprint at 0x100d35f30>,
#  'tutu': 10}
```

- We can import entire module with a different name using `import pandas as pd`

### Using `dir()`
- It is quite similar to `globals()` but instead of listing the object names and their values it just lists the object names.
- `dir` is used for listing the attributes and methods of an object -> [[Python - Everything is an object#Everything is an object| dir usage]].

- Example:
```python
from some_package import some
from pprint import pprint

pprint(list(globals().keys()))
pprint(dir())
print(len(dir()), len(globals().keys()))

# Output:
# Executed some_package.py
# ['__name__',
#  '__doc__',
#  '__package__',
#  '__loader__',
#  '__spec__',
#  '__annotations__',
#  '__builtins__',
#  '__file__',
#  '__cached__',
#  'some',
#  'pprint']
# ['__annotations__',
#  '__builtins__',
#  '__cached__',
#  '__doc__',
#  '__file__',
#  '__loader__',
#  '__name__',
#  '__package__',
#  '__spec__',
#  'pprint',
#  'some']
# 11 11
```
- Both of them list out exactly the same number of keys.

> [!note]- The built-in function `dir()` returns a *list of defined names in a namespace*. Without arguments, it produces an alphabetically sorted list of names in the current **local symbol table**
> If you are running it in the global space then it will list the global symbol table.

- Listing the local symbol table inside a function using `dir()`
```python
def func():
    a = 10
    print(dir())

func()

# Output:
# ['a']
```

- Example: Running `dir()` after adding objects to code
	- ![[attachments/Pasted image 20221218102138.png]]

- *When given an argument that is the name of a module, `dir()` lists the names defined in the module*:
```python
import some_package
from pprint import pprint

pprint(dir(some_package))

# Output:
# Executed some_package.py
# ['__builtins__',
#  '__cached__',
#  '__doc__',
#  '__file__',
#  '__loader__',
#  '__name__',
#  '__package__',
#  '__spec__',
#  'some',
#  'utils']
```

### Reloading a module
- **For reasons of efficiency, a module is only loaded once per interpreter session**.
- That is fine for function and class definitions, which typically make up the bulk of a module’s contents. 
- But a module can contain executable statements as well, usually for initialisation. 
	- Be aware that these statements will only be executed the _first time_ a module is imported.
	- ![[attachments/Pasted image 20221218103224.png]]

- If you make a change to a module and need to reload it, you need to either restart the interpreter or use a function called `reload()` from module `importlib`:
	- ![[attachments/Pasted image 20221218103248.png]]

### Packages
- If a file named `__init__.py` is present in a package directory, **it is invoked when the package or a module in the package is imported**. 
- This can be used for execution of package initialisation code, such as initialisation of package-level data.

>[!note]- **Importing just the package is of NO use** as it  *DOES NOT place any of the modules in `pkg` into the local namespace*.
> ![[attachments/Pasted image 20221218104438.png]]
> ![[attachments/Pasted image 20221218104452.png]]
> - We will have to use something like `import pkg.mod1` or `from pkg import mod1`
> - `dir()` can be used to verify if the module is in the namespace of the caller or not.

- `from package import *` does nothing if unless `__all__` is defined in `package/__init__.py`
- `__all__` can also be used in modules to define what all needs to be imported using `from module import *`
	- ![[attachments/Pasted image 20221218105550.png]]


## References
- [Python Modules and Packages – An Introduction – Real Python](https://realpython.com/python-modules-packages/)
- [Python's Import System - Module object|Regular/Namespace Packages|Finders & Loaders|Relative imports - YouTube](https://www.youtube.com/watch?v=QCSz0j8tGmI)
