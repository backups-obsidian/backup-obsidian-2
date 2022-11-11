---
created: 2022-11-11 09:58
updated: 2022-11-11 10:55
---
---
**Links**: 
- [[108 Python Index]]
- [[Python - Asyncio - Coroutine vs Subroutine]]

---
## Generators
> [!question]- Why use generators?
> - When you want to iterate over a large dataset that can't fit in the memory.
> - Maintaining a state in a function where the function is too simple to use classes instead. Like generating an infinite sequence.

- **Generator functions** are a special kind of function that **return a lazy ITERATOR**.
	- Unlike lists, *lazy iterators do not store their contents in memory*.

- Example: Generating an infinite sequence
```python
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1 # this line maintains the state

a = infinite_sequence()
print(next(a))

for i in a:
	print(i)
```

- We can either use a `for` loop or call `next` to get the values from the generator.
- `yield` indicates where a value is sent back to the caller, but unlike `return`, you don’t exit the function afterward.

- Generator comprehensions
```python
nums_squared_lc = [num**2 for num in range(5)]
nums_squared_gc = (num**2 for num in range(5)) # notice the () brackets
```

- Performance difference between generators and normal lists
```python
import sys
nums_squared_lc = [i ** 2 for i in range(10000)]
sys.getsizeof(nums_squared_lc) # 87624

nums_squared_gc = (i ** 2 for i in range(10000))
print(sys.getsizeof(nums_squared_gc)) # 120
```

- When you *call a generator function or use a generator expression*, you *return a special iterator called a generator*. 
	- You can assign this generator to a variable in order to use it. 
	- When you call special methods on the generator, such as **`next()`, the code within the function is executed up to `yield`**.
	- Example: [[Python - Asyncio - Coroutine vs Subroutine]]
- When the Python yield statement is hit, the program suspends function execution and returns the yielded value to the caller (In contrast, `return` stops function execution completely). 
	- When a function is suspended, the state of that function is saved. 
	- This includes any variable bindings local to the generator, the instruction pointer, the internal stack, and any exception handling.

- **Since generator is an iterator it raises a `StopIteration` exception at the end**.
- Generators can be pipelined.

```python
def fibonacci_numbers(nums):
    x, y = 0, 1
    for _ in range(nums):
        x, y = y, x+y
        yield x

def square(nums):
    for num in nums:
        yield num**2

# sum of squares of numbers in the Fibonacci series
print(sum(square(fibonacci_numbers(10))))
```

- Stopping infinite generators using the generator's `close` method.
```python
def squares_generator():
	# some logic
	yield value

squares = squares_generator()
for x in squares:
	print(x)
	if x > 500:
		squares.close()
```
- Another way of doing it is embedding some kind of logic in the generator using a `return` statement (syntactic sugar for raising `StopIteration` exception) but this would mean that the generator is now not an infinite generator.
	- Example of using `return` statement: [[Python - Asyncio - Coroutine vs Subroutine]]

## References
- [How to Use Generators and yield in Python – Real Python](https://realpython.com/introduction-to-python-generators/) 