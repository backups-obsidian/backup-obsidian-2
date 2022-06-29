---
created: 2022-06-05 11:28
updated: 2022-06-29 16:32
---
---
**Links**: [[103 Golang Index]]

---
- In golang, *both make and new allocate memory*, but there are still some differences between them.
- `new()` just allocates memory, not initialises memory; 
- `make()` allocates and initialises memory. 
- The so-called initialisation is to assign an initial value to a type, for example, the character is empty, the integer is 0, and the logical value is false.

## `new()`
- It **works with any datatype**.
- Definition of the new function : `func new(Type) *Type`
	- The *first argument is a type, not a value*, and the **value returned is a pointer** to a newly allocated zero value of that type.
- The *allocated memory* will be set to zero, that is, the *zero value of the type*, that is, the character is empty, the integer is 0, and the logical value is false
- It doesn't work on channels and map.

```go
type P struct {
	Name string
	Age  int
}
var a *[2]int
var s *string
var b *bool
var i *int
var ps *P

a = new([2]int)
s = new(string)
b = new(bool)
i = new(int)
ps = new(P) //structure

fmt.Println(a, " ", *a)     // &[0 0]   [0 0]
fmt.Println(s, " ", *s)     // 0xc00000e1e0 ""
fmt.Println(b, " ", *b)     // 0xc00001a07a   false
fmt.Println(i, " ", *i)     // 0xc00001a090   0
fmt.Println(ps, " ", *ps)   // &{ 0}   { 0}
```

## `make()`
- It works with **only with slice, channels and maps**.
- `func make(t Type, size ...IntegerType) Type`
	- Like `new()`, the *first argument is a type*, not a value. 
	- Unlike `new()`, `make()` **return type is the same as the type of its argument, not a pointer to it**. 

- **Slice**: 
	- The `size` *specifies the length*. 
	- The capacity of the slice is equal to its length. 
	- A second integer argument may be provided to specify a different capacity; it must be not be smaller than the length. 
	- For example, `make([]int, 0, 10)` allocates an underlying array of size 10 and returns a slice of length 0 and capacity 10 that is backed by this underlying array.
	- `func make([]type, length, capacity) []type` : for slice
- **Map**: 
	- An empty map is allocated with enough space to hold the specified number of elements. 
	- The size may be omitted, in which case a small starting size is allocated.
- **Channel**: 
	- The channel's buffer is initialised with the specified buffer capacity. 
	- *If zero, or the size is omitted, the channel is unbuffered*.

```go
mm :=make(map[string]string)
mm["name"] = "lc"
fmt.Println(mm["name"])

mss :=make([]int,2)
mss[0] = 100
fmt.Println(mss[0])

ch :=make(chan int,1)
ch <-100

fmt.Println(<-ch)
```

## `make()` vs `new()`
```go
b := new([]int)
fmt.Printf("%d, %T, %v, %#v\n", b, b, b, b)
c := make([]int, 3)
fmt.Printf("%d, %T, %v, %#v\n", c, c, c, c)

// &[], *[]int, &[], &[]int(nil)
// [0 0 0], []int, [0 0 0], []int{0, 0, 0}
```

- Since `new` zeroes the value it returns a `nil` pointer, zero value of a slice is `nil`.

```go
nMap := new(map[int][int])
mMap := make(map[int][int])

mMap[0] = 1 // works fine
*(nMap)[0] = 1 // program crashes
// this is because map hasn't been initialised
```