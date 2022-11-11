---
created: 2022-11-11 09:55
updated: 2022-11-11 09:56
---
---
**Links**: 
- [[108 Python Index]]
- [[Python - Asyncio]]

---
## Coroutine vs Subroutines
- Subroutine calling model:
	- Each time a function is called *execution moves to the **start** of that function* then continues until it reaches the end of that function (or a `return` statement)
	- At this point execution moves back to the point immediately after the function call, *any later calls to the function are independent calls which start again at the beginning*.
- Coroutine calling model:
	- In this model there is a new way for the method (called a *coroutine*) to move execution back to the caller: **instead of returning it can `yield` control**.
	- When the coroutine "yields" execution moves back to the point immediately after it was called, but **future calls to the coroutine do not start again at the beginning**, instead they *continue from where the execution left off most recently*. 
	- This way *control can bounce back and forth between the calling code and the coroutine code*.
- Subroutine vs coroutine
	- ![[attachments/Pasted image 20221111093654.png]]

- Code example:
```python
def some_function():
    print("yielding 5")
    yield 5
    print("yielding 10")
    yield 10
    print("returning 15")
    # return just raises a stop iteration excpetion
    return 15

call = some_function()
print(call)
# unless and until you call next() generator function won't be executed
# We call next on the same generator object to return the next item of the iterator

value1 = next(call)
print(value1)
value2 = next(call)
print(value2)
value3 = next(call)

# OUTPUT:
# <generator object some_function at 0x10129fd10>
# yielding 5
# 5
# yielding 10
# 10
# returning 15
# Traceback (most recent call last):
#  File "/Users/sarthaknarayan/Desktop/testfolder/coroutines.py", line 20, in <module>
# value3 = next(call)
# StopIteration: 15

# this piece of code won't stop code execution since for loop knows how to handle StopIteration exception
# for i in call:
#     print(i)
```
- Notice that `return` just raises a `StopIteration` exception in the generator with the return value as the exception message.
	- Ideally return statements are used to terminate generators with a if clause.

```python
def geometric_progression(a, q):
    k = 0
    while True:
        result = a * q**k
        if result <= 100000:
            yield result
        else:
            return
        k += 1

for n in geometric_progression(2,5):
    print(n)
```

- Python has had the capability to allow this execution model for some time in the form of Generators. 
	- But *asyncio adds a new type of coroutine*, which allows a natural way to write code where **execution can move around between coroutines when the current one gets blocked**.
