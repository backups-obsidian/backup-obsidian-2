---
created: 2022-05-22 17:51
updated: 2022-05-28 12:20
---
---
**Links**: [[103 Golang Index]]

---
> [!important] Another key difference between them is that **arrays always passes by value** and **slices always passed by reference**. It's a reason for better performance while using slices.

## Slices
- **Dynamic length**
- *Similarities* between array and slice
	- Both a slice and an array can contain only the *same type of elements*
	- We can create a keyed slice like a keyed array
- *Differences* between array and slice
	- The *length of an array is part of its type*, *defined at compile time* and cannot be changed. The *length of a slice is not part of its type* and it *belongs to runtime*.
	- By **default an uninitialised array** has all elements equal to **zero**. An **uninitialised slice** is equal to **nil** (its zero value is nil). 
```go
array1 := [3]string{}
var array2 [3]string
fmt.Printf("%#v\n", array1) // [3]string{"", "", ""}
fmt.Printf("%#v\n", array2) // [3]string{"", "", ""}

slice1 := []string{}
var slice2 []string
fmt.Printf("%#v\n", slice1) // []string{}
fmt.Printf("%#v\n", slice2) // []string(nil)
```
- `nil` doesn't mean the absence of a value but the value hasn't been initialised yet. 
	- A `nil` slice is an empty slice with 0 capacity.
	- This is why we can call the `len` function on an uninitialised slice.
	- We cannot assign elements to a `nil` slice
```go
var values []string
fmt.Printf("%#v\n", values) // []string(nil)
fmt.Println(len(values)) // 0
values[0] = "test" // runtime error; index out of range
// we could do the above operation with arrays since size was fixed and it was initialised automatically with default values
```
- **Slices cannot be compared** with the `=` operator, they **can only be compared to nil**. 
	- To compare 2 slices we use a for loop to iterate over the slices and compare element by element.

### Creating a slice
- *Uninitialised slice equal to nil*  
	- `var values []int; fmt.Println(value == nil)` - true.  
	- **Declare** a slice but **don't allocated memory** just yet
- *Initialised slice not equal to nil*  
	- `values := []int{}; fmt.Println(values == nil)` - false.
	- Initialise but with no values, **memory is allocated**
- `values := []int{1,2,3}`
- `var values = []int{1,2,3}`
- Using **`make`** : `make(type, len, capacity)`
	- `values := make([]int, 2)` - same as `[]int{0, 0}`
	- The *default values are used to initialise the array*.
- Another way : `type names []string; friends := names{"Dan", "Maria"}`

### Appending values to a slice
- We add values to a slice using `append`.
- This **returns a new slice** with the value appended to it. It **doesn't modify the existing slice**.
- The new slice can be saved back in the same variable.
```go
values1 := []int{1, 2, 3}
fmt.Printf("%p\n", &values1) // 0xc0000ac018

// values1 = append(values1, 45)
values2 := append(values1, 45) 
fmt.Printf("%p\n", &values2) // 0xc0000ac030
fmt.Println(values1, values2) // [1 2 3] [1 2 3 45]
```
> [!caution] In the above example we can't use simple `=` since `values2` hasn't been declared. If you were assigning the `values1` to `append` then using `=` would have been okay

- `append` is a variadic function and *can append more than one value*.
	- `values = append(values, 20,30,40)`
- Another use case of `append` is when we want to append one slice to another slice but we need `...` the end.
```go
values1 := []int{1,2,3}
values2 := []int{4,5,6}
values1 = append(values1, values2...) 
// equivalent to append(values1, values2[0], values2[1], values2[2])
```

### Copying Slices
> [!note] `copy` creates a **separate underlying array for the destination slice**

- Copying one slice to another using `copy(dstSlice, srcSlice)`
- It copies the src slice to destination and *returns the number of elements copied*
- When source and destination are of the same length
```go
values1 := []int{1, 2, 3}
values2 := make([]int, len(values1))
nn := copy(values2, values1)
fmt.Println(nn, values1, values2) // 3 [1 2 3] [1 2 3]
values2[1] = 45
fmt.Println(values1, values2) // [1 2 3] [1 45 3]
```
- When source and destination are of different length
```go
values1 := []int{1, 2, 3}
values2 := make([]int, 2)
nn := copy(values2, values1)
fmt.Println(nn, values1, values2)
// 2 [1 2 3] [1 2]

values1 := []int{1, 2, 3}
values2 := []int{}
nn := copy(values2, values1)
fmt.Println(nn, values1, values2)
// 0 [1 2 3] []
```
- We **can't copy slices using** `:=` or `=` (if the array has been declared) since they will share the *same backing array*.
```go
var b []int
a := []int{1, 2, 3}
b = a // if b was not declared then b := a [:]
fmt.Printf("%p,%p\n", &a, &b) // 0xc00000c048,0xc00000c030
b[1] = 56
fmt.Println(a, b) // [1 56 3] [1 56 3]
```
- If destination is bigger than source then the beginning elements are overwritten
```go
s := []int{1, 2, 3}                
t := make([]int, len(s), cap(s)*2)
copy(t, s)
fmt.Println(t, s) // [1 2 3] [1 2 3]
fmt.Println(cap(t), cap(s)) // 6 3
fmt.Println(t[:cap(t)]) // [1 2 3 0 0 0]
```

### Slicing
- It doesn't modify the original slice/array but instead **returns a new one**.
- It *works for both arrays and slices*.
- Same rules as that of Python slicing. 
	- Missing start index defaults to 0. 
	- Missing end index defaults to maximum length.
	- Element at the start index is included and element at the end index is not included.
```go
values := []int{1,2,3,4,5}
sliced := values[1:3] // [2,3]
```
- *Slicing can be used for copying slices* but they will share the same backing array.
```go
values1 := []int{1,2,3,4,5}
values2 := values1[:]
```
```go
a := []int{1, 2, 3}
b := a[:]
b[1] = 56
fmt.Println(a, b) // [1 56 3] [1 56 3]
```
- Some slice examples
```go
s1 := []int{1, 2, 3, 4, 5, 6}

s1 = append(s1[:4], 100) // adds 100 after index 4 (excluded)
fmt.Println(s1)          // -> [1 2 3 4 100]

s1 = append(s1[:4], 200) // overwrites the last element
fmt.Println(s1)          // -> [1 2 3 4 200]
```
- Removing elements
```go
a := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
a = append(a[:2], a[4:]...)
fmt.Println(a) // [1 2 5 6 7 8 9 10]
// for removing a single element append(a[:i], a[i+1:]...)
```

## [[Go - Slice Working]]