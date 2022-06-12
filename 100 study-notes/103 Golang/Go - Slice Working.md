---
created: 2022-05-28 11:01
updated: 2022-05-28 13:20
---
---
**Links**:
- [[Go - Slices]]
- [[103 Golang Index]]

---
## Slice Working 
- Slice is nothing but a *portion of an array*.

> [!important] Slice occupies less memory than slice

> [!note]- The **backing array stores the elements, not the slice**.

### Slice Header
- **Slice Header is the runtime representation of a slice**
- Go implements a slice as a data structure called **slice header**.
- Slice Header contains 3 fields:
	- The *address* of the backing array (*pointer*). Address pointer of the first element in the *backing array* since elements of the array are contiguous.
	- The *length* of the slice. `len()` returns it.
		- It is the number of elements in the slice
	- The *capacity* of the slice. `cap()` returns it.
		- So *capacity is just the number of elements from the first pointer to the end of the array*. 
```go
values := []int{1, 2, 3, 4, 5}
slice := values[1:3]
fmt.Println(values, slice) // [1 2 3 4 5] [2 3]
fmt.Println(len(values), cap(values)) // 5 5
fmt.Println(len(slice), cap(slice)) // 2 4
// both of them share the same backing array
slice[0] = 56
fmt.Println(values, slice) // [1 56 3 4 5] [56 3]
```
- *Length and capacity are may or may not be same for slices*. In the example below length is 2 and capacity is 3.
	- ![[attachments/Pasted image 20220528103642.png]]
	- If we choose to extend the slice to 9 then both the length and capacity will be 3.
- If we extend the backing array then the capacity will increase without affecting the length of the slice.
	- ![[attachments/Pasted image 20220528103810.png]]
- A nil slice doesn't a have backing array. Its `len` & `cap` is 0.
- Slice Header can be thought of as being represented internally by a structure type.
```go
type slice struct {  
    Length        int
    Capacity      int
    ZerothElement *byte
}
```
- When you *print the address of the slice/array you actually print the address of the pointer* pointing to the starting element. So if two slices have the same starting element they will have the same pointer address value. ^pointerexample
```go 
values := []int8{1, 2, 3, 4, 5, 6} // note int8 here that's why we have 18031 
slice1, slice2, slice3 := values[:4], values[:2], values[1:4]
fmt.Printf("%p,%p,%p", slice1, slice2, slice3)
// arrays/slices are contiguous memory locations
// 0xc000018030, 0xc000018030, 0xc000018031
```

#### Reslicing
> [!caution]- The starting address pointer of the slice can only be moved forward
> ![[attachments/Pasted image 20220528114404.png]]

```go
// moving the address pointer forward
values := []int{1, 2, 3, 4, 5}
slice := values[1:]
fmt.Println(values, slice) // [1 2 3 4 5] [2 3 4 5]
fmt.Println(len(slice), cap(slice)) // 4 4
slice = slice[1:]
fmt.Println(slice) // [3 4 5]
fmt.Println(len(slice), cap(slice)) // 3 3
```

- With the help of `cap` we can *reslice our slice and extend it*. **This is because slicing operations see the whole backing array**.
- But we cannot extend it backwards since address pointer cannot move back.
```go
values := []int{1, 2, 3, 4, 5}
slice := values[1:3]
fmt.Println(values, slice) // [1 2 3 4 5] [2 3]
fmt.Println(len(values), cap(values)) // 5 5
fmt.Println(len(slice), cap(slice)) // 2 4

// reslicing our slice
fmt.Println(slice[:cap(slice)]) // [2 3 4 5]
```

### Creating a new slice
- **A slice expression (slicing) doesn't create a new backing array**, the slice returned after the operation *refers to the backing array of the original slice*. 
	- **Slicing creates a new slice header with the same backing array**.
	- *Starting addresses pointer may or may not be different*. [[Go - Slice Working#^pointerexample|Example]]
```go
// Example that slices share the same backing array
values := []int{1, 2, 3, 4, 5}
v2, v3 := values[0:2], values[1:3]
values[1] = 400
fmt.Println(values, v2, v3) // [1 400 3 4 5] [1 400] [400 3]
```
> [!caution]+ We **cannot create a new slice using slicing**
> - We have to use `copy` since it creates a separate underlying array for the destination slice.
> - We can also use `append` to create a new slice (new backing array) **only if the slice capacity is full**. That is when `len(slice) == cap(slice)`. [[Go - Slice Working#^importantexample|Example]]

### Backing Array 
- When we create a slice using `values := []int{1,2,3,4,5}` behind the scenes 
	- Go first *creates an array* containing elements `[1,2,3,4,5]` which will be used as the backing array. 
	- Go then creates a slice from the created backing array.
- If we create a *slice out of an array* then the first step of creating a backing array is omitted since the array is used as the backing array.

### Internals of Append
- `append` is a not a very efficient operation
- **A new backing array is created if slice capacity is full**. That is when `len(slice) == cap(slice)`
- At the beginning the growth ratio is 2 then it decreases. That is *capacity of the new backing array is double that of the previous backing array*.
	- This is done to avoid creating a new backing array when the next `append()` is called.
- Most important example. ^importantexample
```go
values := []int{1, 2, 3, 4, 5, 6}
fmt.Println(len(values), cap(values)) // 6 6
values2 := append(values, 56)
values[1] = 23

// using different backing array
fmt.Println(values, values2) // [1 23 3 4 5 6] [1 2 3 4 5 6 56]

// the original array didnot expand
fmt.Println(len(values), cap(values)) // 6 6

// the new expanded array
fmt.Println(len(values2), cap(values2)) // 7 12

// since our capacity is 12 we should be using the same backing array for the next append function
values3 := append(values2, 324)
fmt.Println(values2, values3) // [1 2 3 4 5 6 56] [1 2 3 4 5 6 56 324]
fmt.Println(cap(values2), cap(values3)) // 12 12
values3[2] = 90
fmt.Println(values2, values3) // [1 2 90 4 5 6 56] [1 2 90 4 5 6 56 324]
fmt.Println(values2[:cap(values2)]) // [1 2 90 4 5 6 56 324 0 0 0 0]
// hence proved we were using the same backing array for this append
```
```go
letters := []string{"A", "B", "C", "D", "E", "F"}
letters = append(letters[:1], "X", "Y")
fmt.Println(letters, len(letters), cap(letters)) // [A X Y] 3 6
// fmt.Println(letters[4]) -> error as it cannot see the full backing array

// but slicing operations can see the full backing array
fmt.Println(letters[3:6]) // [D E F]
```

### Memory Leak Example
- Slices hold a reference to the underlying array. 
- **As long as the slice is in memory, the array cannot be garbage collected**. 
- This might be of concern when it comes to memory management.
```go
func main() {
	subslice := testSubSlice()
	fmt.Println(subslice, " length: ", "remaining underlying array: ", subslice[:cap(subslice)])
}

func testSubSlice() []int {
	values := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	// do some work
	return values[1:4]
}
// [2 3 4]  length:  remaining underlying array:  [2 3 4 5 6 7 8 9]
```
- The solution for this memory leak is to utilise the `copy` function
- When the function exits the garbage collector will know that original array is not being used anymore
```go
func main() {
	subslice := testSubSlice()
	fmt.Println(subslice, " length: ", "remaining underlying array: ", subslice[:cap(subslice)])
}

func testSubSlice() []int {
	values := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	sub := make([]int, 3)
	copy(sub, values[1:4])
	return sub
}
```

### References
- [Golang Tutorial #13 - Slices - YouTube](https://www.youtube.com/watch?v=KzKNGGoaT5U)
- [Mastering Go Programming : Slicing Slices | packtpub.com - YouTube](https://www.youtube.com/watch?v=fhdA-6LcOxk)