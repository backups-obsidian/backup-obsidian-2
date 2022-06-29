---
created: 2022-05-29 16:01
updated: 2022-06-29 15:24
---
---
**Links**: [[103 Golang Index]]

---
## Receiver Functions/Methods
- Go *doesn't have classes*, but you can **define methods on defined types** (aka **[[Go - DataTypes#Defined Types|Named Types]]**).
	- Methods can be defined of any type and since struct is also a type methods can be defined for structs also.
- These functions are known as *receiver functions or methods*. 
- For receiver method names (similar to `self` or `this` in other OOP languages) keep it short. 
	- Generally it is a convention to have it as the *first letter of type name*
```go
type names []string

// (name type) functions'name
// n is the name of the method receiver also known as receiver
func (n names) example() {
	for _, name := range n {
		fmt.Println(name)
	}
}

func main(){
	friends := names{"a,", "b", "c"}
	friends.example() // a b c
	names.example(friends) // this is not a common way
}
```
-  `n` is the *actual copy* of the names we're working with and is available in the function.
	- Any variable of type names can call this function on itself like variable_name.print()
	- `n` is like `this` or `self` from OOP.

- **A method is a function that takes a receiver as argument**

> [!note]- The major difference between a method and function is that a *method belongs to a type and a function belongs to a package*.

### Using arguments with receiver functions
```go
type names []string

func (n names) example(a int) {
	for _, val := range n {
		fmt.Println(val)
	}
  fmt.Println("This is a", a)
}

func main() {
	friends := names{"a", "b", "c"}
	friends.example(56)
	names.example(friends, 56)
}
```

### Example from Go package
```go
import time

func main(){
	a := 24 * time.Hour 
	fmt.Printf("%T\n", a) 
	// time.Duration, here time is the package name and Duration is the type
	// calling a method on time.Duration type
    // Seconds() is a method aka a receiver function.
    seconds := day.Seconds()
    // Seconds() returns the duration as a floating point number of seconds.
    fmt.Printf("%T\n", seconds) //its type is float64
}
```

### Pointer Receiver
> [!note] Method works on a **copy (pass by value)** except for lists and maps.

> [!caution]- *If one of the methods takes a pointer receiver then it is a good idea to make all the methods as pointer receiver*. 

- Use pointer receivers when you don't want to copy large amounts of value when passing to methods.

> [!tip]- Since methods often need to modify their receiver, *pointer receivers are more common than value receivers*.

```go
type car struct {
	name  string
	price int
}

func carChange1(c car, newPrice int, newName string) {
	c.name = newName
	c.price = newPrice
}

func carChange2(c *car, newPrice int, newName string) {
	(*c).name = newName // can also use c.name, implicit conversion by the compiler
	(*c).price = newPrice
}

// this is also pass by value
func (c car) carChange3(newPrice int, newName string) {
  c.name = newName
  c.price = newPrice
}

// pointer receiver method
func (c *car) carChange4(newPrice int, newName string) {
  (*c).name = newName // c.name would also work, implicit conversion by the compiler
  (*c).price = newPrice
}

func main() {
	myCar := car{name: "test", price: 0}
	carChange1(myCar, 10, "test1")
	fmt.Println(myCar)

	carChange2(&myCar, 20, "test2")
	fmt.Println(myCar)

	myCar.carChange3(30, "test3")
	fmt.Println(myCar)

	(&myCar).carChange4(40, "test4") 
  // myCar.carChange4(40, "test4") would have also worked because of implicit conversion of the compiler
	fmt.Println(myCar)
}

// {test 0}
// {test2 20}
// {test2 20}
// {test4 40}
```

### Method and Pointer Indirection
```go
type Vertex struct {
	X, Y float64
}

func (v *Vertex) Scale(f float64) { // <--- method
	v.X = v.X * f
	v.Y = v.Y * f
}

func ScaleFunc(v *Vertex, f float64) { // <--- simple function
	v.X = v.X * f
	v.Y = v.Y * f
}
```

- Functions with a pointer argument must take a pointer
```go
var v Vertex
ScaleFunc(v, 5)  // Compile error!
ScaleFunc(&v, 5) // OK
```

- Methods with pointer receivers take either a value or a pointer as the receiver when they are called
```go
var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK
```

- For the statement `v.Scale(5)`, even though `v` is a value and not a pointer, the method with the pointer receiver is called automatically. 
- That is, as a convenience, Go interprets the statement `v.Scale(5)` as `(&v).Scale(5)` since the `Scale` method has a pointer receiver.
- Equivalent thing happens in the reverse direction
	- ![[attachments/Pasted image 20220629152323.png]]

### Miscellaneous
> [!caution]- Method declarations are not permitted on named types that are themselves pointer types
> We can create methods only for value named types and pointer named types
```go
type distance *int
func (d *distance) m(){ // this will give an error
	fmt.Println("messages")
} 
```
