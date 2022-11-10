---
created: 2022-10-29 11:42
updated: 2022-11-07 21:49
---
---
**Links**: [[108 Python Index]]

---
## Understanding args and kwargs
- `*args` eats all the positional arguments
- `**kwargs` eats all the positional arguments
```python
def f(a,b,*args, **kwargs):
	print(a,b,args,kwargs)

def f1(*args, **kwargs):
	print(args,kwargs)

def f2(a,b,*args, kwa, **kwargs):
	print(a,b,args,kwa,kwargs)

f(1,2,3,4,5) # 1 2 (3, 4, 5) {}
f(1,2,3,4,kw=5) # 1 2 (3, 4) {'kw': 5}
f(1,2,3,4,kw=5, abc=6) # 1 2 (3, 4) {'kw': 5, 'abc': 6}

f1(1,2,3,4,kw=5, abc=6 # (1, 2, 3, 4) {'kw': 5, 'abc': 6}

f2(1,2,3,kwa=1,abc=34) # 1 2 (3,) 1 {'abc': 34}
```

## Positional and Keyword Arguments
```python
def f(a, b, c):
	pass

f(1, 2, 3) # only positional requirements
f(a=1, b=2, c=3) # only keyword requirements
f(c=3, a=1, b=2)  
f(1, c=3, b=2) 
f(c=3, 1, b=2) # SyntaxError
```

- We can have keyword arguments in any order.

> [!caution]- We can have positional arguments with keyword arguments but the **positional arguments** need to be passed **first**.

- This means that the functional caller has a lot of flexibility on how he/she wants to pass the arguments.

```python
def f(a, b, *, c):
	pass

f(1,b=2,c=3)
f(1,2,c=3)
f(1,2,3) # syntax error
# things after the star have to be passed as keyword arguments
```
- Everything after the `*` is a key word argument.
	- To remember just know that everything after `*args` eats all the positional arguments which means it is logical to have only keyword arguments after that.

- *The advantage of using `*` over `*args` to force keyword arguments is that if you provide any extra arguments then it is swallowed by `*args` but `*` will give an error*.
	- This gives option to accidentally send extra parameters.
```python
def f(a, b, *, c):
	pass

f(1,2,3,c=3) # syntax error
```

- **Force all the parameters to be keyword only**
```python
def f(*,a,b):
	print(a,b)

f(a=1,b=2)
```

- Full syntax
```python
# zero examples of this in python library
def f(only_pos_args, /, pos_or_kw_args, *, only_key_word_args ):
	pass

# arguments can now be either positional or keyword but not both.
# example code (dataclass)
def f(only_pos_args, /, *, only_key_word_args ):
	pass

# only positional arguments
def f(only_pos_args, /):
	pass

# only keyword arguments
def f(*, only_key_word_args ):
	pass
```

- Don't work with only positional arguments.
- Only keyword and positional keyword example but not both
	- ![[attachments/Pasted image 20221029121317.png]]

### Use of `**` and `*` operator for arguments
- Argument expansion
```python
def some_func(a, b):
    print(a, b)

some_dict = {"a": 1, "b": 2}
some_func(**some_dict) # 1 2
```

> [!note]- Notice there is a subtle difference between above and `**kwargs`. 
> `**kwargs` are used at the time of function definition whereas `**some_dict` is used at the time of function call.
> `**kwargs` mean catch all whereas `**some_dict` means send the key value pairs to the function.

- Another example:
```python
def some_func(a, b):
    print(a, b)

some_dict = {"a": 1, "b": 2, "c": 3}
some_func(**some_dict) # TypeError: some_func() got an unexpected keyword argument 'c'
```

```python
def some_func(a, b, **kwargs):
    print(a, b)
    print(kwargs)

some_dict = {"a": 1, "b": 2, "c": 3}
some_func(**some_dict) 
# 1 2
# {'c': 3}
```

- Similarly we can expand list for positional arguments using `*`
```python
def some_func(a, b, c):
    print(a, b, c)

l_l = [1, 2, 3]
some_func(*l_l) # 1 2 3
```

- Catching extra variables in args
```python
def some_func(a, b, c, *args):
    print(a, b, c)
	print(args)


l_l = [1, 2, 3, 4]
some_func(*l_l)
# 1 2 3
# (4)
```


## References
- [Positional-only and keyword-only arguments in Python - YouTube](https://www.youtube.com/watch?v=R8-oAqCgHag)