---
created: 2022-05-21 15:26
updated: 2022-06-05 11:28
---
---
**Links**: 
- [[103 Golang Index]]
- [[Go - Basics]]

---
## DataTypes
### Basic Types
- `int8`, `int16`, `int32`, `int64`
- `uint8`, `uint16`, `uint32`, `uint64`: used to represent *unsigned (positive) integers*.
- `uint` is an **alias** for `uint32` or `uint64` based on platform.
- `int` is an **alias** for `int32` or `int64` based on platform.
- `float32`, `float64`: zero before the decimal point separator can be omitted (-.5-3.-0.
1.4).
- complex64, complex128.
- `byte` : *alias for uint8*
- `rune` : alias for int32
```go
var a rune = 'f'
fmt.Printf("%T",a) // int32
fmt.Println(a) // 102 (decimal ascii code of f)
```

> [!note] Golang *doesn't have a character type* it uses byte and rune to distinguish characters from integers.

> [!tip] Underscores are used to write large numbers for a better readability. `var i3 int64 = -324_567_345`

> [!caution] **Double quotes** should be used when working with *strings*

### [[Go - Strings, Runes & Bytes]]
### Composite Types
- **Array** has a **fixed** length, **slice** has a **dynamic** length.
	- `var numbers = [4]int{1,2,3,4}` - Array
	- `var numbers = []float32{1.1,2.2}` - Slice
	- We can also use walrus operator: `number := []string{"hi","ti"}`
- *Map is just like dictionary* in Python
```go
test := map[string]int{
	"hi": 34,
	"test": 45,
}
```
- **Struct Type** (*User defined type*)
	- A struct is a sequence of named elements, called fields, each of which has a name and a type.
	- A struct *can be compared to class* concept in Object Oriented Programming.
```go
type Car struct {
	brand string
	price int
}
var tesla Car
fmt.Printf("%T",tesla) // main.Car
```
- **Pointer Type**
	- A pointer is a variable that *stores the memory address of another variable*
	- The value of an *uninitialised* pointer is `nil`
	- `var x := 45; ptr := &x` - Type of ptr is `*int`

### Converting Types
```go
var x int8 = 4
var y float32 = 5.6
var z = x * int8(y) //20
```
- In the above example **y is not modified but a new int value is returned**
- int to string is done using `Sprintf` : `x := fmt.Sprintf("%d",45)`
- Unicode conversion : `x := string(65)` - A
- Converting string to int/float/bool : `var f1, err = strconv.ParseInt("45",32)` - second parameter is the precision.
- `Atoi`(string to int) and `Itoa`(int to string)
```go
i, err := strconv.Atoi("-50")
s = strconv.Itoa(20)

// => i Type is int, i value is -50
fmt.Printf("i Type is %T, i value is %v\n", i, i) 

// => s Type is string, s value is "20"
fmt.Printf("s Type is %T, s value is %q\n", s, s) 
```

### Defined Types
- A defined type also called a **named type** is a new type created by the programmer from another existing type which is called the underlying or source type
- A new defined type must have : new name and can have its new methods
- The underlying type provides the representation, operations and size of the newly defined type
- **Even though the defined type and the source type share the same representation, operations and size they are different types**. *A new type it's not just an alias for an existing type, it's a completely new type*.
	- In the below example underlying type of speed is uint8.
```go
type speed uint8
var x speed = 89
fmt.Printf("%T", x) // main.speed
var y uint8 = x // error since x is of type main.speed
var y unit8 = unit8(x) // no error
```
- If we want to perform operations between source and defined types we must convert one type into the other type. **A type can be converted to another type if they share the same underlying type**.
- There is *no type-hierarchy* in Go

> [!question]- Why do we need defined types?
> - We can *attach methods to newly defined types*
> - **Type safety**: We must convert one type into another to perform operations with them.
> - **Readability**: When we defined a new type let's say `type usd float64` we know that new type represents the US Dollar not only floats

```go
package main

type usd float64

func main() {
	var balance usd = 50.5
}
// usd is a named type and balance a variable of usd type
```

### Aliases
- An alias declaration has the form: `type T1=T2` as opposed to a standard type definition which has the form: `type T1 T2`
- An alias declaration binds an identifier to the given type. **It's the same type with a new name**.

> [!note] *Types with different names are different types*, but there is an *exception* to this rule and that is the **aliased types**.

- `byte` and `uint8` *are aliases or the same type with different names*. The same is applicable to `rune` and `int32` because rune is an alias for int32.
- Aliases can be used together in operations *without type conversions* we've seen at defined types

> [!caution] You should use aliases with *caution*, they are *not for everyday use*!