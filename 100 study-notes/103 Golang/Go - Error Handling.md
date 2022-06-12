---
created: 2022-05-29 16:01
updated: 2022-06-12 19:10
---
---
**Links**: [[103 Golang Index]]

---
## Error handling
- In Go **we don't have exceptions** 
	- This is because a lot of things which are thought of as exceptions in other languages are normal in go.
- We use `panic` when our application can't continue to function.
- We can use the panic function if we will that our program can't continue due to some reason.
```go
func main(){
	fmt.Println("hello")
	panic("quitting") // some logic in our program because of which we think it should not continue execution
	fmt.Println("end")
}
// hello
// quitting
```
- Generally go will return and error, it is upto the developer to decide whether we should panic or not.

> [!note]- Panics happen *after deferred statements are executed*.
> - This is important to know because any defer statements that were going to close resources are going to succeed even if our application panics.
> - If somewhere up the call stack you recover from panic you don't have to worry about resources being left open.

- We can also *recover from panic*. 
	- Our program crashes only if panic reaches the go runtime. 
	- Go runtime doesn't know how to deal with panic.
- **Recover is only useful inside deferred functions**
```go
func main () {
	fmt.Println("start")
	panicker()
	fmt.Println ("end")
}
func panicker () {
	fmt.Println ("about to panic")
	defer func() {
		if err recover (); err != nil {
		log.Println("Error:", err)
		}
	}()
	panic("something bad happened")
	fmt.Println("done panicking") // not printed
}
// start
// about to panic
// Error : something bad happened
// end
```
- In the above example our *panicker function stops execution at panic*, and now prepares to execute any deferred functions 
	- Inside the defer function we recover from panic by calling `recover`
	- The function that recovers (panicker) still stops execution because it is in a state where it can no longer reliably continue to function so it makes sense to stop doing what it was trying to do.
	- However the functions higher up the call stack (the functions that called panicker) can continue because recover was called in the panicker.
	- If you realise that the error is something you can't recover from then you can repanic the application i.e. rethrow panic and manage it higher up the call stack if possible
```go
func panicker () {
	fmt.Println ("about to panic")
	defer func() {
		if err recover (); err != nil {
		log.Println("Error:", err)
		panic("cannot recover")
		}
	}()
	panic("something bad happened")
	fmt.Println("done panicking") // not printed
}
```

### Returning errors
- We could have panicked and stopped the execution of program but this is a better approach
	- We let the caller know something wrong has occurred and let it handle it.
- This is very *idiomatic in go*.
```go
func main () {
	d, err := divide(5.0, 0.0)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(d)
}
func divide (a, b float64) (float64, error) {
	if b == 0.0 {
		return 0.0, fmt.Errorf("Cannot divide by zero")
	}
	return a/b, nil
}
```

### Miscellaneous
> [!note]- Don't throw panic unless there is no way of recovering from the situation
> - For example if you are running a web server on port 8080 and that port is occupied then you have to throw a panic
> - But suppose you are making a get request using the http package and you get a 404 response in that case you should handle it instead of using panic.