---
created: 2022-11-03 10:29
updated: 2022-12-06 16:52
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

## Formatting fstrings
```python
# formatting using f strings

text = "text"

# left allignment
print(f"{text:<20} How are you?") # without how are you? you wouldn't have noticed the allignment
print(f"{text:>20}")
print(f"{text:^20}") # centre allignment
# here 20 is the total number of spaces including the text (4 characters)

# we can add any symbol we want
print(f"{text:-^20}") # centre allignment
# if you want spaces before and after text just add them in the string
# for eg: text = " text "
```

- Output:
	- ![[attachments/Pasted image 20221206163418.png]]

## `is` vs `==`
- `is` is for instance comparison whereas `==` is for value comparison.
	- If you want to compare values like `a == b` use `==`
	- If you want to compare 