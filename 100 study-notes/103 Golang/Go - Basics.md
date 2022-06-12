---
created: 2022-05-15 21:47
updated: 2022-05-29 11:46
---
---
**Links**: [[103 Golang Index]]

---
## Features of Go
- Go is a **statically typed** programming language which means it does *type checking at compile time*. Some other statically typed languages are C, C++, Java.
	- In case of Go you can explicitly declare the types or it *can be inferred during compile time*.
- A *dynamically typed language* like Python or PHP *does type checking at runtime*.
- Go is also a **strongly typed** language which means you cannot assign a type to a variable with another type.
	- You would need type casting to do this `var a,b = 10,4.5; a = int(b)`
	- You *cannot multiply int with float*.

## [[Go - Variables]]
## [[Go - Constants]]
## [[Go - DataTypes]]
## Naming
- Convention is to use **camelCase** for writing multi word names instead of snake_case. This is applicable to *variables*, *functions* and *constants*. `maxValue := 343`
- Acronyms should be in all caps : `writeToDB := true`
- By convention *packages* are given *small case single word names*

> [!caution] *Don't use variables with capital letters* in the program as they will be exported by Go to be used in other packages.

## Miscellaneous
- `x++`, `x--` works
- `==` equality check
- `a < 5 && b > 10` - second operation is evaluated only if first one is true. *short circuit*.
- A *float number overflows to infinity*

## fmt package
- `Println` : 
	- `a := "test"; fmt.Println("hello", a)` - hello test
	- Spaces are always added between operands and a newline is appended
- `Printf` : 
	- The *first argument is the template string* that contains the text you want to format plus some *annotation verbs* (Eg: `%d`) that tell the function *how to format the trailing arguments*.
	- Number of verbs should be equal to the number of passed values.
		- `%d` - integer. d comes from decimal
		- `%f` - float. `%.3f` - only show 3 decimal points
		- `%s` - string
		- `%q` - double-quoted strings. Eg: `a := "hello"; fmt.Printf("How %q",a)` - How "hello"
		- `%v` - can be **replaced by any value**
		- `%#v`- a Go-syntax representation of the value
		- `%+v` - to print the struct's values and fields
		- `%T` - print the *type of the variable* 
		- `%t` - for *boolean* values
		- `%p` - for *pointer* address. Address in base 16, with leading 0x
- `Sprintf` - returns a string. Uses the same verbs as `Printf()`
	- `s := fmt.Sprintf("hello %d", a)`
