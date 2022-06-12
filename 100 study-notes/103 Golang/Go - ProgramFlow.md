---
created: 2022-05-21 16:43
updated: 2022-05-28 09:42
---
---
**Links**: [[103 Golang Index]]

---
## [[Go - If & Switch]]
## [[Go - For Loops]]
## Command line arguments
- We get command line arguments using the `os.Args` package. 
- It returns a slice of strings where the first value is the absolute path of the executable file.
- Accessing the elements : `os.Args[1]`
- **Arguments are always strings**.

## Labels
- Labels are used in `break`, `continue`, and `goto` statements.
- It is *illegal to define a label that is never used*.
- In contrast to other identifiers, *labels are not block scoped* and **do not conflict with identifiers that are not labels**. They **live in another space**.
- The scope of a label is the body of the function in which it is declared and excludes the body of any nested function.
- Most of the time labels are *used to terminate outer enclosing loops*.
```go
a1 := [5]int{1, 2, 3, 4, 5}
b1 := [4]int{5, 6}

outer:
	for _, value1 := range a1 {
		for _, value2 := range b1 {
			if value2 == 6 {
				fmt.Println(value2, value1)
				break outer
			}
		}
	}
	fmt.Println("after outer")

// 6 1
// after outer
```

## GoTo
- It allows us to *jump to any label inside the same function*

> [!important] `break` and `continue` statements are *restricted* to be used only in *for* and *switch* statements but `goto` statements *don't have this restriction*

- Creating a loop like a for statement
```go
i := 0
loop:
	if i < 5 {
		// statements
		i++	
		goto loop
	}
```

- Not possible to jump over variable declarations
```go
goto todo // this will give an error
x := 10 
todo: 
	// statements
```

> [!caution] **Discouraged** to use goto statements

## Scope 
- Scope means **visibility**.
- The scope or the lifetime of a variable is the interval of time during which it exists as the program executes.
- *A name cannot be declared again in the same scope* (for example a function in the package scope), but it can be declared in another scope.
- In Go there are 3 Scopes: 
	- **File Scope**: You can import a package only once although you can import it again if you use aliases (`import f "fmt"`). Basically the name should be unique. 
	- **Package Scope**: Variables declared outside of the function are *available for use to all the files in the package*.
	- **Local/block Scope**: The variables defined in the function are local/block scoped.
	
```go
package main
import "fmt" //file scope
const done = false //package scope
func main () {
	x := 10 //local (block) scope
	fmt.Println(x)
}
```

> [!important] *Unused package scoped variables don't return an error* since they can be used in other files of the same package, whereas unused local/block scope variables do.