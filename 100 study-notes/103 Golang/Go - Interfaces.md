---
created: 2022-06-05 11:28
updated: 2022-06-05 13:31
---
---
**Links**: [[103 Golang Index]]

---
## Interfaces
- An interface is a **collection of method signatures** that an object (struct) must implement.
	- Interfaces only have the signatures and not their implementations
- *Interfaces define the behaviour of an object*.
- Interfaces can achieve **polymorphism**.
	- Can take many dynamic forms at runtime
- Interfaces are just tools to make the code more readable, whether we use them or not is upto us.
- To implement an interface in Go we just need to implement all the methods in the interface.
	- Go interfaces are implemented implicitly.
	- You *don't have to explicitly mention if a named type implements an interface*.
	- In other OOP languages like Java we have to use `implements` keyword to implement an interface.

> [!important]- If a `type` implements all the methods in the interface then that type is said to implement that interface. This makes go special.
> Its like saying if it walks like a duck, swims like a duck and quacks like a duck then its a duck.

- If a variable is of the interface type then we can call methods that are in the interface type.

> [!caution] **Any type that implements the interface is also of the type interface**.

> [!note] A *type can implement one or more interfaces*.

- The **type that implements an interface must implement all the methods otherwise we will get an error**.
	- A *type can have other methods that don't belong to the interface*.

### Example
```go
type circle struct {
	radius float64
}

type rectangle struct {
	length, breadth float64
}

type shape interface {
	area() float64
	perimeter() float64
}

func (c circle) area() float64 {
	return c.radius * c.radius * 3.14
}

func (c circle) perimeter() float64 {
	return c.radius * 2 * 3.14
}

func (r rectangle) area() float64 {
	return r.length * r.breadth
}

func (r rectangle) perimeter() float64 {
	return r.breadth * r.length * 2
}

// It can take in any type that implements the interface
func print(s shape) {
	fmt.Printf("%T\n", s)
	fmt.Println(s)
	fmt.Println(s.area())
	fmt.Println(s.perimeter())
}

func main() {
  c1 := circle{radius: 1.}
  r1 := rectangle{length: 2., breadth: 4.}
  print(c1)
  print(r1)
}

// main.circle {1} 3.14 6.28
// main.rectangle {2 4} 8 16
```

- Zero value of an interface type is `nil`
```go
func main() {
	var s shape // abstract value
	fmt.Printf("%T\n", s)
	c1 := circle{radius: 1.} // concrete value
	s = c1
	fmt.Printf("%T\n", s)
	print(s)
	// var s shape = circle{radius: 1}
}
// <nil>
// main.circle
// main.circle {1} 3.14 6.28
```

- Interfaces have dynamic types that can change during runtime.

### Type Assertions and Type Switches
- **Type assertion provides access to an interface's dynamic concrete value**.
- We *can only access the methods defined in the interface*
```go
func (c circle) volume() (float64) {
	// return
}

func main() {
	var s shape = circle{radius: 1}
	fmt.Printf("%T\n", s)
	s.volume() // this will give an error
	// type assertion
	s.(circle).volume()
}
```
- To get ***access to the underlying dynamic type we use Type Assertion***.
	- It will extract and return the dynamic value of the interface value
- *Type Assertions can fail* so it is a good practice to check if it succeeded.
```go
ball, ok := s.(circle)
if ok {
	fmt.Printf("%v", ball.volume())
}

ball, ok := s.(int) // this would fail
```
- Type Switch
```go
var s shape = circle{radius: 1}
switch value := s.(type) {
	case circle:
		// statements
	case rectangle:
		// statements
}
```

### Embedded Interfaces
- In go **an interface cannot implement other interfaces or extend them**
	- We can create a new interface by merging two or more interfaces, this is known as embedding interfaces.

```go
type details interface {
	name()
}

type address interface {
	location()
}

type employee interface {
	details
	address
	salary()
}
```
- Circular embedding of interfaces will give compile time error

### Empty Interface
- An empty interface has *no methods inside it*.
- **Any go type will satisfy the empty interface**.
	- It is a key concept in go
	- An empty interface may hold values of any type

```go
type empty interface{}

func main() {
	// anything can be stored in an empty interface
	var emp empty // we could have also used var emp interface{}

	emp = 56
	fmt.Printf("%T\n", emp)

	emp = "hello"
	fmt.Printf("%T\n", emp)

	// remember we cannot perform operations on interfaces we would need the concrete/dynamic value for this
	// We need to perform type assertion
	// fmt.Printf("%v\n", len(emp))  error
	fmt.Printf("%v\n", len(emp.(string)))
}

```

> [!question]+ Why are empty interfaces used?
> Empty interfaces are used by code that handle values of **unknown types**.

```go
type person struct {
	info interface{}
}

p1 := person{}
p1.info = "hello"
p1.info = 45
```

> [!caution] Empty interfaces are being used to bypass type safety of go. Use empty interfaces only if it is necessary.

- Getting the dynamic value in empty interfaces 
```go
type cube struct {
	edge float64
}

func volume(c cube) float64 {
	return c.edge * c.edge * c.edge
}

func main() {

	var x interface{}
	x = cube{edge: 5}

	// Type Assertion
	// cannot use x (variable of type interface{}), need type assertion
	v := volume(x.(cube))

	fmt.Printf("Cube Volume: %v\n", v)

}
```