---
created: 2022-05-21 20:19
updated: 2022-05-21 21:46
---
---
**Links**:
- [[103 Golang Index]]
- [[Go - ProgramFlow]]

---
## If condition
- It is not necessary to enclose the testing condition in parenthesis.
- `a == true` is same as just `a` in if condition
- **Test conditions must always yield bool values**
```go
// this code is valid in python and java but not in go
price := 45
if price {
	fmt.Println("error")
}
```
- Syntax
```go
if a > 45 {
	// statements
} else if a < 54 {
	// statements
	// you can have multiple else ifs 
} else {
	// statements
}
```

### Simple If
- `val` and `err` are variables **scoped to the if statement only**
```go
if val, err := strconv.Atoi("45"); err == nil {
	fmt.Println(err)
} else {
	fmt.Println(val)
}

// the above is equivalent to 
val, err := strconv.Atoi("45")
if err == nil{
	// ..
} else {
	// ...
}
```
- Example of scoping
```go
if a := 56; a > 40 {
	fmt.Println("hello")
} else {
	fmt.Println("tello")
}
fmt.Println(a) 
// error : undefined: a
```

## Switch Statements
> [!note] Go **converts switch statements to if statements** behind the scenes
- The purpose of switch statements is to make *very long if statements readable*
- Go *adds a break statement* to each case close *automatically*. In other programming languages an explicit break is mandatory.
```go
language := "go"
switch language {
	case "python":
		// statements
	case "go", "golang":
		// its like using the logical or operator	
	default:
		// equivalent to else clause
}
```
- The `default` clause is equivalent to the `else` clause in if statements
- The `default` clause is **not mandatory**.
```go
n := 5
// comparing bool to another bool value 
switch true { // equivalent to just switch
	case n%2 == 0:
		// statements
	case n%2 != 0:
		// statements
}
```