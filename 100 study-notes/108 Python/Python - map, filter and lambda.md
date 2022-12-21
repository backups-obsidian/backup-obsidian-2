---
created: 2022-12-17 11:52
updated: 2022-12-19 21:52
---
---
**Links**: [[108 Python Index]]

---
## `lambda`
- They are generally used in `map` and `filter` functions.
- *lambda functions are single line functions defined without a name*.
- Syntax: `lambda arg1, arg2, ... : arg1 + arg2 + ...` - any action on arguments that can be done in one line and will be returned.
- Using lambda without `filter` or `map` doesn't make sense since we can use `def` for defining functions.
	- ![[attachments/Pasted image 20221219213315.png]]

- Sorting using lambda functions

```python
some_dict = {"key1": 10, "key2": 5, "key3": 22}

sorted_dict = sorted(some_dict.items(), key=lambda item: item[1])
# using item[1] sorts the dictionary based on values
# using item[0] would have sorted the dictionary based on keys
print(dict(sorted_dict))
```

## `filter`
- It takes **2 arguments**: *a function and an iterable*. 
	- *It then applies the function on each item of the iterable*.
	- **The function should return a boolean value**.
	- Filter filters out the items for which the function returns True.
	- If it returns some kind of number or string, it will always be considered as a truthy value since numbers represent True (except 0) in python.

> [!note]- `filter` returns a filter object which is iterable.
> We can either iterate over it using a loop or get its values using `list(filter(...))`

```python
def filter_func(item):
    if item % 2 == 0:
        return True
    return False


numbers = list(range(1, 10))

x = filter(filter_func, numbers)
print(list(x))

# or

x = filter(lambda item: item % 2 == 0, numbers)

# Output:
# 2 4 6 8
```

- Note that we just pass the reference to the function.

## `map`
- It *takes a function and **one or more** iterables*. 
	- `map` will apply the function on each iterable passed to it.
	- For passing multiple iterables the function should accept multiple items.
- `map` also returns a map object which is iterable.

```python
numbers = list(range(1, 10))

x = map(lambda item: item**2, numbers)
print(list(x))

# Output:
# [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

- Multiple iterables
```python
numbers = list(range(1, 10))

x = map(lambda item1, item2: item1**2 + item2 // 2, numbers, numbers)
print(list(x))
```
