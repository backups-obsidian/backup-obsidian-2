---
created: 2022-05-29 16:01
updated: 2022-06-12 19:38
---
---
**Links**: [[103 Golang Index]]

---
> [!question]- Why use pointers instead of returning values?
> - When we pass values to functions a copy of them is passed. 
> - This isn't problematic when you are passing simple strings or ints which are small in size 
> - But if you are passing large data structures to functions a copy of it passed which is a waste of resources and might slow down your program 
> - In these cases it is better to pass pointers to these large data structures

## Pointers
- The Computer Memory (RAM) can be thought of as a sequence of boxes or cells, placed one after another in a line. 
- Each cell is labeled with a unique number (hexadecimal), which increments sequentially; this number is the address of the cell or its memory location.
	- ![[attachments/Pasted image 20220529190604.png]]
- Each cell holds a single value. Everything the CPU does is fetching and storing values into memory cells. 
- If a number starts with `0x` then it means it is a hexadecimal number and most likely an address.

### What is a variable
- A **variable is just a convenient, alphanumeric nickname or label for a memory location**.
- When we write `var a int = 5` we create a *label called a for a memory location where the value 5 of type int will be stored*.

> [!note]- In a nutshell: memory is just a series of numbered cells, and variables are just nicknames for memory locations assigned by the compiler.

### What is a pointer
- **A pointer is a variable that stores the memory address of another variable**.

> [!important]- The *pointer points to memory address of a variable*, just as a *variable represents the memory address of a value*.
- A pointer value is the address of a variable or `nil` *if it hasn't been initialised* yet.

- In the below image, variable `b` has value `156` and is stored at memory address `0x1040a124`. The variable `a` holds the address of `b`. Now `a` is said to point to `b` or `a` is a pointer to `b`.
	- ![[attachments/Pasted image 20220529191204.png]]

- **Pointers have the power to mutate or change the data they are pointing to**. 
	- With a pointer we can read or update the value of a variable directly without knowing the name of the variable.

## Coding
- To get the memory address of a variable we use `&`
- Pointer is represented as `*type`
```go
age := 15
ptr := &age
fmt.Println(ptr, &age) // 0xc000018030 0xc000018030
fmt.Printf("%#v, %T\n", ptr, ptr) // (*int)(0xc000018030), *int
// to get the address where pointer is stored
fmt.Println(&ptr) // 0xc00000e028
```
- **Declaring a pointer**
```go
var ptr1 *int
// this pointer hasn't been initialised yet and its default value is nil
fmt.Println(ptr1) // nil
```
- Another way of creating a pointer is using the `new` keyword
	- `ptr1 := new(int) // int pointer`
- **Dereferencing operator** (`*`)
	- To find the value at the address pointer is pointing to
```go
age := 15
ptr := &age
fmt.Println(*ptr, age) // 15 15 
*ptr = 45
fmt.Println(age) // 45
fmt.Printf("%T", *ptr) // int
```
- `*` is also used for dereferencing apart from declaring the pointer
	- `*float64` (*type declaration*) is completely different from `*p`
- We can have a pointer to a pointer
```go
a := 10
p1 := &a
pp1 := &p1
fmt.Println(*p1) // 10
fmt.Println(**pp1) // 10
**pp1++
fmt.Println(a) // 11
fmt.Printf("%T",pp1) // **int
```
- *Comparing pointers*
	- Pointers are *same* if both of them are `nil` or they *point to the same* _**variable**_
```go
a := 5
b := 5
p1 := &a
p2 := &b
fmt.Println(p1 == p2) // false
```

### Passing pointers to functions
- Go allows to paste parameters to functions both by pointers and values
- If we want to *change* `int`, `float`, `bool`, `string`, `arrays` and `struct` values in a function rather than their copies then we will have to *pass a pointer to the function*.
```go
func f1(a *int) *float64 {
	*a = 56
	b := 10.23
	return &b
}

func main() {
	value := 1
	fmt.Println(value) // 1
	ptr := f1(&value)
	fmt.Println(value) // 56
	fmt.Println(*ptr) // 10.23
}
```
- Passing struct as a pointer
```go
type person struct {
	name string
	age  int
}

func f1(p *person) {
	(*p).name = "abc"
	p.age = 45 // same as (*p).age = 45, shortcut offered by go
}

func main() {
	p1 := person{
		name: "sarthak",
		age:  23,
	}
	fmt.Println(p1) // {sarthak 23} 
	f1(&p1)
	fmt.Println(p1) // {abc 45}
}
```
- But in case of **slice** and **map** when the function changes data actual data is changed.

> [!tip]- It is not a good practice to pass arrays to functions instead we should pass slices.

> [!note]+ In Go everything is **passed by value**
> - In other languages when a pointer is passed to a function it is known as pass by reference and this is not correct.
> - Each time we pass an argument to a function *even if its a pointer that argument will be copied to a different memory location*.

```go
func f1(a *int) {
	fmt.Println("Should be same in f1", a)
	fmt.Println("Different in f1", &a)
}

func main() {
	value := 1
	ptrval := &value
	fmt.Println("Should be same in main", ptrval)
	fmt.Println("Different in main", &ptrval)
	f1(ptrval)
}
// Should be same in main 0xc000018030
// Different in main 0xc00000e028
// Should be same in f1 0xc000018030
// Different in f1 0xc00000e038
```

> [!important] Pass pointers to functions only when it is necessary. Pointers are expensive and put pressure on garbage collector.