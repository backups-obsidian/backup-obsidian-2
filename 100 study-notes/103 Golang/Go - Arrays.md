---
created: 2022-05-15 21:47
updated: 2022-05-28 10:33
---
---
**Links**: [[103 Golang Index]]

---
## Arrays
- **Fixed length** collection of **same elements**. Since its fixed length a lot of errors can be caught at compile time.
- The length and the elements type determines the type of an array. The *length* belongs to array type and it's *determined at compile time*.
- Different ways of declaring and initialising arrays
	- `var values = [4]int{}` - all 0's in arrays
	- `var values = [2]int{1,2}`
	- `values := [2]int{1,2}`
	- `values := [3]int{1}` - initialising only 1st element of the array, other elements are the default values of their type.
- *Only declaration*: `var values [4]int`. By *default* it is initialised with *0's*
	- To add values to it we do `values[0] = 3`
- **Ellipsis operator** to *automatically find the length of the array*
	- `var values = [...]int{1,2,3,4,5}`
- We use *inbuilt function* `len` to return the number of elements in an array. `len(values)`
- Initialising an array in multiple lines for better readability an *ending comma* is mandatory.
```go
values := [...]int{
1,
2,
3, // this comma is mandatory
}
```
- Array modification - `values[2] = 45`
- Multidimensional array
```go
var numbers = [2][3]int{
	{1, 2, 3},
	{4, 5, 6}, // comma is important since essentially this is a multi line array
}
```
- *Two arrays are equal* if they have the same elements, same length and same order of elements.

### Iteration
- For **iterating** over an array there are *2 ways*: `range` and `len`
```go
for index, value := range values {
	// statements
}
// c++/java style
for i := 0; i < len(values); i++ {
	// statements
}
```

### Copy 
> [!important]- `:=` or `=`(if the array has already been declared) creates a **copy of the array** in a different memory location. This is not possible with slices.
```go
m := [3]int{1, 2, 3}
n := m //n is a copy of m
fmt.Println("n is equal to m: ", n == m) // => true
m[0] = -1                                //only m is modified
fmt.Println("n is equal to m: ", n == m) // => false
fmt.Println(n,m) // [1,2,3] [-1,2,3]
```
```go
var a = [3]int{1, 2, 3}
var b [3]int
fmt.Printf("%p, %p\n", &a, &b) // 0xc000014018, 0xc000014030
b = a
fmt.Printf("%p, %v\n", &b, b) // 0xc000014030,[1 2 3]
b[1] = 10
fmt.Println(a, b) // [1 2 3] [1 10 3]
```

### Keyed elements
- `values := [3]int{1:3}` - initialising values of specific elements. 
- The keyed elements can be in any order - `values := [3]int{2:3, 0:56}`
- `values := [...]int{5:56}` - This implies the *array will have 6 elements*.
- An un-keyed element gets its key from the last element.
```go
values := [...]string{
	5: "london",
	"NYC",
	1: "paris"
}
// length of the array is 7 and index of NYC is 6
```
- Keyed elements are useful since they automatically populate the default values. For example if you want to have an array of weekends to be true and weekdays to be false then `weekends := [...]bool{5:true, 6:true}`

### Array Working
- An array consists of three things: a *pointer* (pointing to the first element of an array), *capacity* and *length*. 
- For an array **length == capacity** always