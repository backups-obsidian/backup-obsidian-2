---
created: 2022-12-17 12:31
updated: 2022-12-17 12:55
---
---
**Links**: [[108 Python Index]]

---
## Namespaces
> [!caution]- Don't use `global` and `nonlocal` keywords.

### Introduction
- A namespace is a *collection of currently defined symbolic names* along with information about the object that each name references. 
	- *Everything is an object in python*.
	- The statement `x = foo` creates a symbolic name `x` that refers to the string object `foo`.  
	- You can think of a **namespace as a dictionary** in which the **keys are the object names** and the **values are the objects** themselves.

### 4 Types of namespaces in python
#### Built-In
- Contains the names of all of Python’s built-in objects. 
- These are *available at all times* when Python is running.
- We can get it using `dir(__builtin__)`
	- Some of the objects that we may recognise are `max()`, `len()`

#### Global
- The global namespace contains *any names defined at the level of the main program*.
- Python creates the global namespace when the main program body starts, and it *remains in existence until the interpreter terminates*.  
- The built-in function `globals()` returns a reference to the current global namespace dictionary. 
	- You can use it to access the objects in the global namespace. 
	- Here’s an example of what it looks like when the main program starts:
		- ![[attachments/Pasted image 20221217122313.png]]
- Defining a variable in the global scope:
	- ![[attachments/Pasted image 20221217122555.png]]
	- After the assignment statement `x = 'foo'`, a new item appears in the global namespace dictionary. 

> [!important]+ Strictly speaking, this *may NOT be the only global namespace that exists*. 
> - The **interpreter also creates a global namespace for any module that your program loads with the `import` statement**.

#### Enclosing & Local
- Interpreter *creates a new namespace whenever a function executes*. 
- That namespace is **local to the function** and remains in existence until the function terminates.
- In the below example namespace created for `inner_func()` is the **local namespace**, and the namespace created for `outer_func()` is the **enclosing namespace**.

```python
def outer_func():
	print("outer function")

    def inner_func():
		print("inner function")

    inner_func()

outer_func()
```

- Python also provides a corresponding built-in function called `locals()`. It’s similar to `globals()` but *accesses objects in the local namespace instead*:
	- ![[attachments/Pasted image 20221217122728.png]]
	- Notice that, in addition to the locally defined variable `s`, the local namespace includes the function parameters `x` and `y` since these are local to `f()` as well.

> [!tip]- If you call `locals()` outside a function in the main program, then it behaves the same as `globals()`.

### Namespace scoping rules
- LEGB rule:  *local*, *enclosing*, *global*, and finally the *built-in* scope 
	- **Local**: If you refer to `x` inside a function, then the interpreter first searches for it in the innermost scope that’s local to that function.
	- **Enclosing**: If `x` isn't in the local scope but appears in a function that resides inside another function, then the interpreter searches in the enclosing function's scope.
	- **Global**: If neither of the above searches is fruitful, then the interpreter looks in the global scope next.
	- **Built-in**: If it can't find `x` anywhere else, then the interpreter tries the built-in scope.

> [!note]- If the interpreter *doesn't find the name in any of these locations, then Python raises a `NameError` exception*.

- Some examples:  

```python
# Example: Enclosing scope
def outer_func():
    x = 10
    def inner_func():
        print(x)

    inner_func()

outer_func() 
# Output: 10
```

```python
# Example: Local Scope
def outer_func():
    x = 10
    def inner_func():
        x = 20
        print(x)

    inner_func()

outer_func() 
# Output: 20
```

```python
def outer_func():
    x = 10
    def inner_func():
        print(y)

    inner_func()

outer_func()

# Output: NameError y is not defined
```

### References
- [Namespaces and Scope in Python – Real Python](https://realpython.com/python-namespaces-scope/)