---
created: 2022-11-03 10:29
updated: 2022-11-07 16:45
---
---
**Links**: [[108 Python Index]]

---
## Use of ellipsis operator (...)
- When arguments of the function are of type any.

```python
def func(abc: ...) -> None:
	pass
```

- Used as pass statement inside functions

```python
def func() -> None:
	...
```

## Extending an array in python
```python
a = [1, 2, 3]

b = [10] + a
print(b) # [10, 1, 2, 3]

# similar to JS ... operator
c = [10, *a]
print(c) # [10, 1, 2, 3]

e = [*a, 10]
print(e) # [1, 2, 3, 10]

d = [10]
d.extend(a)
print(d) # [10, 1, 2, 3]
```