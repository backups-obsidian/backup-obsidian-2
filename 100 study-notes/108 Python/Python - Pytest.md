---
created: 2022-12-20 19:13
updated: 2022-12-22 22:12
---
---
**Links**: [[108 Python Index]]

---
## Pytest
- In pytest **tests are written as functions**.
- You want all of you tests to be independent of each other so that they can be run independently.

> [!note]+ Requirements for `pytest` to find the tests. If the naming convention is not met then `pytest` won't be able to find the tests.
> - The *file should start with `test_` or end in `_test`*.
> - *The functions must be prefixed with `test_`*.
> - It is a general convention to keep all the tests within the `tests` folder but this is not mandatory.

- Just running `pytest` will execute all the tests. 
	- Executing a specific test file: `pytest tests/test_specific.py`
	- Executing a specific function in a test file: `pytest tests/test_specific.py::function_name`
	- Running in verbose mode `pytest -v`

- By default pytest only prints something when the tests fail. 
	- But if we want pytest to print something then we use `pytest -s` 
	
- *We can have multiple asserts in a single test function and they will be considered as single test*.
	- **Each function is a single test** irrespective of the assert statements inside it.

- **Each test is quite small and self-contained**. 
	- This is common you’ll *see long function names and NOT a lot going on within a function*.
	- This serves mainly to *keep your tests isolated from each other, so if something breaks, you know exactly where the problem is*. 
	- A nice side effect is that the labelling is much better in the output.

> [!tip]- It is always a good idea that after passing your tests you make them fail intentionally.

### Fixtures
- **Fixtures are functions which give data to other functions**. 
	- This concept is simple yet most powerful in pytest framework.
- These **fixtures run before and then execution of test function follows**.
- A function can declared as a fixture by, 

```python
import pytest 

@pytest.fixture 
def input_value(): 
	string = "python" 
	return string

```

- So, *fixture function name is passed as arguments for corresponding test functions* and used where ever needed. 
- Here, in fixture function, string variable is returned. 
	- This is accesses by rest of the test functions , instead of repeating the same code again and again.

```python
def test_upper(input_value): 
	assert input_value.upper() == "PYTHON" 
	
def test_isalpha(input_value): 
	assert input_value.isalpha() == True
```

> [!tip]- The major advantage of using fixtures reduces the code complexity, length of code and cost as well. 
> For example, while establishing database connection, we can make use of these fixtures to code data regarding setting up connection in one place and make use of it where ever needed.

- In `pytest`, fixtures are **modular**. 
	- Being modular means that *fixtures can be imported*, *can import other modules*, and they *can depend on and import other fixtures*.
	- If we want to make a fixture available for your *whole project without having to import it*, a special configuration module called `conftest.py` will allow you to do that.

- Another interesting use case for fixtures and `conftest.py` is in guarding access to resources. 
	- Imagine that you’ve written a test suite for code that deals with API calls. 
	- You want to ensure that the test suite doesn’t make any real network calls even if someone accidentally writes a test that does so.
	- `pytest` provides a `monkeypatch` fixture to replace values and behaviours, which we can use to great effect

```python
# conftest.py

import pytest
import requests

@pytest.fixture(autouse=True)
def disable_network_calls(monkeypatch):
    def stunted_get():
        raise RuntimeError("Network access not allowed during testing!")
    monkeypatch.setattr(requests, "get", lambda *args, **kwargs: stunted_get())
```

- By placing `disable_network_calls()` in `conftest.py` and adding the `autouse=True` option, you ensure that network calls will be disabled in every test across the suite.
	- Any test that executes code calling `requests.get()` will raise a `RuntimeError` indicating that an unexpected network call would have occurred.

## Not Done
- Markers.
- Test parameterisation.
- Skipping tests.

## References
- [Effective Python Testing With Pytest – Real Python](https://realpython.com/pytest-python-testing/)
- [PyTest • REST API Integration Testing with Python - YouTube](https://www.youtube.com/watch?v=7dgQRVqF1N0)
- [Pytest Unit Testing Tutorial • How to test your Python code - YouTube](https://www.youtube.com/watch?v=YbpKMIUjvK8)