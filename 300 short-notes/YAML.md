---
created: 2022-05-27 11:53
updated: 2022-05-27 12:09
---
---
**Links**: [[300 home]]

---
### Basics
```yaml
name: Joe
age: 35
```
```json
{
    "name": "Joe",
    "age": 35
}
```
---
```yaml
names:
    - name 1
    - name 2
    - name 3
```
```json
{
    "names": ["name 1", "name 2", "name 3"]
}
```

### Objects of Objects
```yaml
name: Joe
age: 35
address:
  streetAddress: 123 No Street
  city: Phantom City
```
```json
{
    "name": "Joe",
    "age": 35,
    "address": {
        "streetAddress": "123 No Street",
        "city": "Phantom City"
    }
}
```

> [!note] You leave an empty space after `:` if you want to nest objects within objects

```yaml
person:
 details: 
  name: Fred
  age: 34
```
```json
{
  "person": {
    "details": {
      "name": "Fred",
      "age": 34
    }
  }
}
```

### Array of Objects
```yaml
person:
 - name: Fred
   age: 45
 - name: Albert
   age: 16
```
```json
{
  "person": [
    {
      "name": "Fred",
      "age": 45
    },
    {
      "name": "Albert",
      "age": 16
    }
  ]
}
```

> [!note] `-` is followed by a `:` hence that value becomes an object inside the array

```yaml
person:
 - name: Fred
 - name: Albert
 - name: Jay
   age: 45
 - names: Last
 - nothing
```
```json
{
  "person": [
    {
      "name": "Fred"
    },
    {
      "name": "Albert"
    },
    {
      "name": "Jay",
      "age": 45
    },
    {
      "names": "Last"
    },
    "nothing"
  ]
}
```

> [!note] In the above example last entry doesn't have `:` after it so its just an entry in the array and not an object.

### Miscellaneous
```yaml
input: value
# the above is same as
input:
	value
```