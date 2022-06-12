---
created: 2022-05-21 12:15
updated: 2022-05-28 09:31
---
---
**Links**: 
- [[103 Golang Index]] 
- [[Go - Basics]]

---
## Variables in GO
- **Variables belong to runtime**
	- Example: `x,y := 6,0; fmt.Printf(x/y)` - This will throw run time error.
- In GO **all variables are initialised**. So even if you don't initialise the declared variables they will get a default type. Example: `var value int; fmt.Println(value)` - 0
	- integer = `0`
	- float = `0.0`
	- bool = `false`
	- string = `""` (empty string)
	- pointer = `nil`
- Multiple statements can be separated in Go using `;`
- There are **2 ways** of **declaring variables** in Go. 
	- `var` keyword: 
		- Syntax: `var var_name type` - `var test string`
		- `var age int = 10` - **type can be omitted as it can be inferred by Go during compile time** hence we can omit `int` here.
		- `var name = "test"`	
	- **walrus operator** (`:=`): declaring and initialising 
		- `s := "hello"`
		- *Cannot be used for already defined variables*
		- Walrus operator *only works in block scope*. Can't be used at package scope (outside main or any other function)

> [!caution] *Type of a variable cannot be changed* once declared.

> [!caution]- *Unused variables* generate compile time errors.
> This can be solved by using `_` which is used to *mute the compiler*. It is always used on the left hand side.
> `var name = "test"; _ = name` 

- Use `var` or walrus operator if you want to *create variables*. If you want to change the value of variables then use `=`. `name = "hello"` where name has been created.

> [!important] You *cannot redeclare variables* in a block.

### Multiple Declaration
- Multiple variable declaration: `car, price := "audi", 3900`
	- Cannot be used to redefine variables unless one of the variables is new
		- `car, price := "audi",399; car, price := "test", 343` - this would give an *error*
		- `car, price := "audi", 455; car, new_var := "test", true` - this is fine
			- The second statement is equivalent to `car = "test";var new_var = true`
- Multiple declaration using var
	- `var a, b int`: concise way
		- Tuple assignment: `var a, b; a, b = "test", 65`. 
	- The below syntax is *good for readability*
```go
// elaborate way
var (
	name string
	age int
	salary float64
)

// we can also initialise them
var (
	a int = 45
	b string = "hello"
)
```

> [!note] We use multiple variable declaration using walrus operator when we know the value of variables otherwise we use the var.