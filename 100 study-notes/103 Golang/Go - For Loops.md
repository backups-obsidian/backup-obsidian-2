---
created: 2022-05-21 20:32
updated: 2022-05-21 21:46
---
---
**Links**:
- [[103 Golang Index]]
- [[Go - ProgramFlow]]

---
## For Loops
- There is **only for loop** in go.
- `continue` and `break` statements have the same effect as in any other language.
```go
for i := 0; i<10; i++ {
	fmt.Println(i)
}
```
- Third statement is flexible in go
```go
// ; after i < 10 is important
for i := 0; i<10; {
	fmt.Println(i)
	i++
}
```
- While loop using for
```go
i := 0
for i < 10 {
	fmt.Println(i)
	i++
}
```
- Infinite loop
```go
for {
	// statements
}
for i := 0; true; i++ {
	// statements
}
```
- Handling multiple variables in a for loop
```go
for i, j := 0, 100; i < 10; i, j = i+1, j+1 {
	fmt.Printf("i = %v, j = %v\n", i, j)
}
```
- Iterating over an array
```go
for index, value := range someArray {
	// statements
}
// if you don't want index you can use _ instead of index
```

