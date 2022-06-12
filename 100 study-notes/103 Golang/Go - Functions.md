---
created: 2022-05-15 21:47
updated: 2022-06-05 10:06
---
---
**Links**: [[103 Golang Index]]

---
## Functions
- Go recommends writing function names in simple word or **camelCase**.

> [!caution]- Within the same package function names must be unique!
- One of Go's features is that functions and methods **can return multiple values**.
- Go *doesn't support function overloading*.
- In Go functions are first class citizens. This means
	- Functions can be assigned to variables
	- Can be passed as arguments to other functions
	- Or return from other functions

### Defining functions
```go
func f1(a int, b int){
	// statements
}
f1(3,5)
```
- Shorthand parameter notation
```go
func f1(a,b int, c,d float64) {
	// statements
}
f1(1,2,3.4,4.5)
```

> [!caution] There are **no default arguments** in Go.

### Returning values
- If the function is returning something its return type must be specified
- Simple return
```go
func f1(a int) int {
	return a
}
```

#### Multiple return
- If `error` is returned then as a convention it should be the last parameter
- For multiple return values type must be inside parenthesis
```go
func f1(a int) (string, int) {
	// statements
	return "hello", 54
}
a, b := f1(3)
_, d := f1(5) // if you are interested in only one value 
```

#### Named Return
- We can return the named parameters without any value. 
- This is known as *naked return* and should *only be used in short functions* as it harms the readability in long functions.	
```go
// behind the scenes it does var s string inside the function
func f1(a int) (s string) {
	fmt.Println(s, a) // "" 45 -> we get default value since it has been declared
	s = "hello"
	return // naked return, same as return s
}
val := f1(45)
fmt.Println(val) // hello
```
- We can also return multiple values with named return
```go
func f1(a int) (s string, b int) {
	s = "hello"
	b = 100
	return
}
val, b := f1(45)
fmt.Println(val, b) // hello 100
```

### Variadic Functions
- Variadic functions take variable number of arguments
	- We use variadic function when the number of arguments is unknown
- Ellipsis prefix (three-dots) in front of the parameter type makes a function variadic.
- The function may be called with **0 or more parameters**
- If the function takes *parameters of different types* then **only the last parameter of the function can be variadic**.
```go
func f1(a ...int, string b) {} // this is wrong
func f1(b string, a ...int) {} // this is correct
```
- `fmt.Println` is an example of variadic function
- We can paste a slice to a variadic function by postfixing it with the variadic operator (`...`)
	- You can think it as taking out all the values of slice and then passing them to the function
```go
// here type of a is []int
func f1(a ...int) {
	fmt.Printf("%T, %#v\n", a, a)
}
f1(1, 2, 3, 4) // []int, []int{1, 2, 3, 4}
f1() // []int, []int(nil)
slice := []int{12, 13, 14}
f1(slice...) // []int, []int{12, 13, 14}
```
- We cannot pass arrays to variadic functions
	- variadic functions use slices and `[]int != [4]int`
```go
func f1(a ...int) {
	a[0] = 100
}
slice := [4]int{1, 2, 3, 4}
f1(slice...) 
// error -> cannot use slice (variable of type [4]int) as type []int in argument to f1
// notice the types in error
```
- Example
```go
func main() {
	fmt.Println("Hello world")
	b := []int{1, 2, 3, 4}
	test(b...)
	fmt.Println(b) // {54, 2 ,3, 4}
}
func test(a ...int) {
	a[0] = 54
}
```

### Defer
- A defer statement postpones the execution of a function until the surrounding function returns.
- If there are *multiple deferrals* then go will execute them in the reverse order of their deferral. Think of it as going from *bottom to top* (only for the deferred functions).
- The *arguments of the deferred functions are evaluated immediately* but not executed until the surrounding function returns.
- `defer` statement is used to make sure some function is executed later for cleanup like when opening a file we defer the file close function.
```go
func f1() {
	fmt.Println("This is function 1")
}

func f2() {
	fmt.Println("This is function 2")
}

func f3() {
	fmt.Println("This is function 3")
}

func main() {
	defer f1() // 5
	f2() // 1
	fmt.Println("penultimate statement") // 2
	defer f3() // 4
	fmt.Printn("last statement") // 3

}
// This is function 2
// penultimate statement
// last statement
// This is function 3
// This is function 1
```

### Anonymous Function
- These functions don't have any name and are *declared inline*.
- These can be used to *form closures*.
```go
func (a string) {
	fmt.Println(a)
}("hello")
```
```go
// saying that we are returning a function of type int
func increment(a int) func() int {
	// returning an anonymous function
	return func() int {
		a++
		return a
	}
	// we are not executing the function immediately as in above example
}

func main() {
	inc := increment(10)
	fmt.Printf("%T\n", inc) // func() int
	fmt.Printf("%#v\n", inc) // (func() int)(0x47f6c0)
	inc()
	fmt.Println(inc()) // 12
}
```