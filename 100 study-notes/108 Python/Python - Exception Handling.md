---
created: 2022-11-13 15:52
updated: 2022-11-21 21:43
---
---
**Links**: [[108 Python Index]]

---
## Exception Handling
### try, except and finally
- *Exceptions are propagated through the call stack in unhandled*.
	- For example let us consider a program where we have a function  `A` that calls function `B`, which in turn calls function `C`. 
	- If an exception occurs in function `C` but is not handled in `C`, the exception passes to `B` and then to `A`.
	- If never handled, an error message is displayed and our program comes to a sudden unexpected halt.
- We handle exceptions using `try except` block.

> [!note]- Almost every class inherits from the `Exception` class.

- Catching multiple and specific exceptions
```python
try:
   # do something
   pass

except ValueError:
   # handle ValueError exception
   pass

except (TypeError, ZeroDivisionError):
   # handle multiple exceptions
   # TypeError and ZeroDivisionError
   pass

except Exception as e:
   # handle all other exceptions
   pass
```

- We can raise exceptions using the `raise` keyword.
	- Raising a general exception: `raise Exception("some message")`
	- Raising a specific exception: `raise ValueError("Some thing is wrong")`

- `finally` clause is executed no matter what:
```python
try:
   f = open("test.txt",encoding = 'utf-8')
   # perform file operations
finally:
   f.close()
```

- Handling vs Raising exceptions:
	- ![[attachments/Pasted image 20221121182647.png]]

### Custom Exceptions
```python
class GitHubApiException(Exception):
    def __init__(self, status_code):
        if status_code == 403:
            message = "Rate limit reached. Please wait a minute and try again."
        else:
            message = f"HTTP Status Code was: {status_code}."

        super().__init__(message)
```

- Or we can have a *custom message*
```python
class IncorrectValueError(Exception): 
	def __init__(self, value): 
		message = f"Got an incorrect value of {value}"
		super().__init__(message)
```

- Or have multiple parameters and print them as a single message:
```python
class CustomException(Exception):
    def __init__(self, ext_message: str, code: int) -> None:
        message = f"Some {ext_message} with {code}"
        super().__init__(message)
```

### Assertions
> [!important]- Assertions are most useful in tests.

- Using assertions
	- Assertions are used to raise an exception if a condition is not met.

```python
import sys
assert ('linux' in sys.platform), "This code runs on Linux only."

# if the program is run on a different system then we will get the following error
Traceback (most recent call last):
  File "<input>", line 2, in <module>
AssertionError: This code runs on Linux only.
```

- Handling assertions gracefully
```python
def linux_interaction():
    assert ('linux' in sys.platform), "Function can only run on Linux systems."
    print('Doing something.')

try:
    linux_interaction()
except AssertionError as error:
    print(error)
    print('The linux_interaction() function was not executed')

# running it on windows system will give the following output
# Function can only run on Linux systems.
# The linux_interaction() function was not executed
```

> [!question]- What is the difference between `raise` and `assert`?
> - `raise` allows you to throw an exception at any time.
> - `assert` enables you to verify if a certain condition is met and throw an exception if it isn’t.

> [!caution]- Don't use assertions as your only line of defence as they can be turned of using `python -O main.py`
> Use exceptions

## References
- [python try except and best practices (beginner - intermediate) anthony explains #359 - YouTube](https://www.youtube.com/watch?v=tIh42X0oGQc)
