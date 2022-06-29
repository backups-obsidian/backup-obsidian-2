---
created: 2022-06-29 19:25
updated: 2022-06-29 19:29
---
---
**Links**: 

- [[103 Golang Index]]
- [[Go - Interfaces]]

---
## Interface : Value and pointer receivers
- Example with value receiver
```go
type myStruct struct {
}

func (p1 myStruct) print1(a int) {
	fmt.Println(a)
}

func (p2 myStruct) print2(a string) {
	fmt.Println(a)
}

type myInterface interface {
	print1(int)
	print2(string)
}

func main() {
	var ms myInterface = myStruct{} // value type for implementing an interface
	fmt.Println(ms)
}
```

> [!important]- When implementing an interface if you use a value type, all the methods should have value receivers. If we are implementing an interface with a pointer then we should just have the methods there.
> Even if none of the functions were pointer receivers, using a pointer would have worked.

```go
func (p2 *myStruct) print2(a string) {
	fmt.Println(a)
}
// this would give an error since we have a pointer receiver

// to fix this we can do
var ms myInterface = &myStruct{}
```

### Another Example
> [!note]- In the below example `Vertex` (the value type) doesn't implement `Abser` because the `Abs` method is defined only on `*Vertex` (the pointer type).

```go
type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat implements Abser
	a = &v // a *Vertex implements Abser

	// In the following line, v is a Vertex (not *Vertex)
	// and does NOT implement Abser.
	a = v

	fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```