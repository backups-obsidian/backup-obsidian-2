---
created: 2022-12-14 17:44
updated: 2022-12-14 21:37
---
---
**Links**: [[108 Python Index]]

---
## Correct way of running modules
- Sample project structure

```
├── file1.py
└── test-module
    ├── __init__.py
    └── file2.py
```

- Code inside `file1.py`

```python
# file1.py

print("hello from file 1")
file1 = 10
```

- Code inside `file1.py`

```python
import sys
import pprint

pprint.pprint(sys.path)

from file1 import file1

print("hello from file2")
print(file1)
```

- Now if I try to run `python test-module/file2.py` it fails with the following error

```
['/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python310.zip',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/lib-dynload',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/site-packages']
Traceback (most recent call last):
  File "/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module/file2.py", line 6, in <module>
    from file1 import file1
ModuleNotFoundError: No module named 'file1'
```
- If we notice carefully we can see that the path from where python starts to look for files starts from `/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module` so there is no way it can find `file1.py` since it is outside of `test-module`
- **The correct way of running `file2.py` is by using `-m` (module) flag in python**.
- Running `python -m test-module.file2` 

```
['/Users/sarthaknarayan/Desktop/testfolder/module-test',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python310.zip',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/lib-dynload',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/site-packages']
hello from file 1
hello from file2
10
```

- If we notice the path again we can see that this time our path is `/Users/sarthaknarayan/Desktop/testfolder/module-test` so python can find `file1.py` which is being imported in `file2.py`

## Wrong way of solving the above problem using `PYTHONPATH`
- We get no errors if we run `PYTHONPATH=$(pwd) python test-module/file2.py`

```
['/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module',
 '/Users/sarthaknarayan/Desktop/testfolder/module-test',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python310.zip',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/lib-dynload',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/site-packages']
hello from file 1
hello from file2
10
```

- The main problem with this approach is that the path `/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module` is still present and it is the first location from where python looks for packages.
- This can lead to problems later if we wish to have 2 modules/files with the same name.
- Suppose my directory structure is something like this

```
.
├── file1.py
└── test-module
    ├── __init__.py
    ├── file1.py
    └── file2.py
```

- then `PYTHONPATH=$(pwd) python test-module/file2.py` will give the following error

```
['/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module',
 '/Users/sarthaknarayan/Desktop/testfolder/module-test',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python310.zip',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/lib-dynload',
 '/Users/sarthaknarayan/.pyenv/versions/3.10.0/lib/python3.10/site-packages']
Traceback (most recent call last):
  File "/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module/file2.py", line 6, in <module>
    from file1 import file1
ImportError: cannot import name 'file1' from 'file1' (/Users/sarthaknarayan/Desktop/testfolder/module-test/test-module/file1.py)
```

- Note that it is trying to import `file1.py` from `test-module` instead of importing it from the root directory.

## Another Example
- Directory structure
```
.
├── module1
│   ├── __init__.py
│   └── file1.py
└── module2
    ├── __init__.py
    └── file2.py
```

- Python files
```python
# file1.py
some_variable = 1

# file2.py
from module1 import file1

print(file1.some_variable)
```

- `python -m module2.file2` works fine.

## References
- [don't run `python my/script.py`! (beginner - intermediate) anthony explains #407 - YouTube](https://www.youtube.com/watch?v=hgCVIa5qQhM)