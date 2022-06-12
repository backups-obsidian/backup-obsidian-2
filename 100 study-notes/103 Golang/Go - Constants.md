---
created: 2022-05-21 12:16
updated: 2022-05-21 12:35
---
---
**Links**: 
- [[103 Golang Index]] 
- [[Go - Basics]]

---
## Constants
- A **constant belongs to the compile time** and it's created at compile time. It's value can not be changed while the program is running.
	- Example: `x,y := 6,0; fmt.Printf(x/y)` - This will throw run compile time error.
- Another advantage of using constants is that Go can not detect runtime errors at compile-time but constants belong to compile time so errors can be detected earlier
- Constants *can be unused* unlike variables and compiler won't give any error.
- Multiple declarations
	- `const n1, m1 int = 5,6`
	- `const n1, m1 = 5,6`
	- Grouped constant
```go
const (
	m1 int = 45
	m2 string = "hello"
)
```

- In a grouped constant a constant repeats the previous one
```go
const (
	m1 = 600
	m2
	m3
)
// m2 and m3 will also be equal to 600
```

### Rules for constants
- Variables can be left uninitialised after declaring but *constants must be initialised after declaring them*.

> [!caution]- You cannot initialise the constants at runtime
> - `const a int = Math.pow(3,4)` - This is *incorrect since function calls belong to runtime*

- You *cannot use variables to initialise constants*. Eg: `t := 6; const tc = t` - Incorrect. This is because variables belong to runtime.
- We can use built in functions like `len`. `const a = len("hello")`
- You can use expressions if they are constant expressions since it can be evaluated at compile time.
```go
const a = 10
const b = 20
const c = a*b
```

> [!caution]- You cannot declare constants of type array
> There are ONLY boolean constants, rune constants, integer constants, floating-point constants, complex constants, and string constants.

### Typed and Untyped Constants
- `const a int = 65` - Typed constant
- `const a = 56` - Untyped constant
```go
// constants
const x = 5
const y float64 = 1.1
fmt.Println(x * y)
// 5.5, No Error because x is untyped and gets its type when its used first time (float64).

// variables
var v1 = 5
var v2 float64 = 1.1
fmt.Println(v1 * v2)
// invalid operation: v1 * v2 (mismatched types int and float64)
```

> [!tip] Untyped constants give us greater flexibility

### Iota 
- It is **number generator** for constants
- `iota` keyword represents successive keyword integers.
- By **default** iota starts with **0**.
- `_` can be used to skip values
```go
const (
	a = iota
	b = iota
	c = iota
)
// a = 0, b = 1, c = 1

// same as above since grouped constants are repeated.
const (
	a = iota
	b
	b
)

// using steps
const (
	a = (iota * 2) + 1
	b
	c
)
// a = 1, b = 3, c = 5
```

