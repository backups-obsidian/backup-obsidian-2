---
created: 2022-11-03 10:29
updated: 2022-11-11 20:28
---
---
**Links**: [[108 Python Index]]

---
## What are wheels?
- Wheel is a prebuilt package installable by just unzipping the archive and not running any code.
- Wheels are fancy zip files that have `whl` as extension
	- We can unzip them using the `unzip` command: `unzip file_name.whl`

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

## Partial Functions
```python
from functools import partial

def adder(a: int, b: int, c: int) -> int:
    return a + b + c

adder_a = partial(adder, c=12, b=3)
adder_a_b = partial(adder, c=5)

print(adder_a(10)) # 25
print(adder_a_b(a=3, b=5)) # 13
print(adder_a_b(a=3, b=5, c=8)) # 16
```