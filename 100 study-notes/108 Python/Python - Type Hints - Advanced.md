---
created: 2022-09-16 16:24
updated: 2022-10-27 15:41
---
---
**Links**: [[108 Python Index]]

---
## Type Hints
### Literals
- `from typing import Literal`
- ![[attachments/Pasted image 20221027153655.png]]
- ![[attachments/Pasted image 20221027153950.png]]
- Example:

```python
from typing import Literal

def test(a: Literal[5]) -> None:
    print(a)

test(a=5)
# we can only pass 5 to the function and not any other int
```
