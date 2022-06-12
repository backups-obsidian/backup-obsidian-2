---
created: 2022-05-15 21:47
updated: 2022-05-28 21:12
---
---
**Links**: [[103 Golang Index]]

---
## Maps
- It is like an object in JS or dictionary in Python.
- The main advantage of maps is that **add, get and delete operations take constant expected time**.
	- This is because maps are backed by hash tables
- All the **keys and the values** in a Map are **statically typed** and must have the **same type**.
	- Keys and values don't have to be of the same type. We can have keys of type string and values of type integers.
- The **keys in a map must be unique**, but the values don't have to be unique.
- We can use **any comparable type as a key map**. 
	- A comparable type is that type that supports the comparing operator which is the double equals sign.
	- Even if it's possible, it's *not recommended to use a float as a key*. A float has some comparable issues.
	- Slices are non comparable and cannot be used as keys for maps whereas **arrays are comparable and can be used as keys for maps**.
		- `var test map[[5]int]string`
	- Comparable types: string, int, bool, arrays
- We *cannot compare a map to another map*.
	- We *can only compare a map to nil*.
- Maps are **unordered data structures** in Go.
- Declaring Maps
	- Zero value of a map is **nil**
```go
var employees map[string]string
fmt.Print("%#v",employees) // map[string]string(nil)
```
- To find the number of key value pairs in the map we use `len`

### Missing Keys
- We can get the value of a key in a map even if it does not exist. 
	- There is no error
	- If the key does not exist in the map it returns the default value of that type
```go
var employees map[string]string
fmt.Println(employees["DAN"]) // "" -> empty string
```
- But sometimes we need to differentiate between the default value and non existent key
	- This can be done using the , ok syntax
	- `value, ok = employees["DAN"]`
	- There `ok` contains a bool value. `true` if the key is present in the map otherwise its `false`

### Inserting values in a map
- We cannot populate an uninitialised map. That is you cannot assign to a nil map. 
```go
// this is an uninitialised map
var employees map[string]string
// employees["DAN"] = "45" -> this will give an error
```
- Initialising declared maps
```go
var employees map[string]string
employees = map[string]string {
	"DAN": "45"
}
```
- We can populate an initialised map 
	- This is an **empty but an initialised map**
```go
people := map[string]string{}
people["DAN"] = "54"
// or 
var people = map[string]string{}
```
- We can also use `make` to create an *empty but initialised map*
```go
map1 = make(map[int]int)
map1[4] = 8
```
- Initialising the map while declaring it
```go
people := map[string]string{
	"Dan":"45",
	"Test": "43", // this , is important. This was same for multi line arrays and slices
}
// or
var people = map[string]string{"Dan":"45"}
```

> [!note] If the key exists its value is updated. If the key doesn't exist it is created.

### Cloning a map
- When a map variable is declared, Go creates a pointer to a map header in memory.
- When a map is copied to another map using `=` or `:=` just the pointer is copied and not the elements. So the new map also points to the same elements
```go
friends := map[string]int{"dan": 54, "maria": 32}
neighbours := friends
neighbours["dan"] = 1
fmt.Println(friends) // map[dan:1 maria:32]
// neighbours and friends have the same map header
```
- For copying a map we have to loop over the existing map and assign key and values to the new map
```go
friends := map[string]int{"dan": 54, "maria": 32}
people := map[string]int{}
for key, value := range friends {
	people[key] = value
}
people["dan"] = 1
fmt.Println(friends, people) // map[dan:54 maria:32] map[dan:1 maria:32]
```

### Miscellaneous
- **Looping** over maps
```go
for key, value := range map1 {
	// statements
}
```
- **Deleting** a key value pair from map
	- It is not necessary to check if the key exists or not before calling the `delete`. There is no error even if the key doesn't exist.
	- `delete(mapName, "key")`
- We know maps cannot be compared. One way of comparing them is converting them into strings and then comparing them
	- This can be done when keys and values are of type string
```go
m1 := map[string]string{"A": "B"}
m2 := map[string]string{"C": "D"}
s1 := fmt.Sprintf("%s",m1)
s2 := fmt.Sprintf("%s",m2)
if s1 == s2
```