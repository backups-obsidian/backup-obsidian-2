---
created: 2022-06-18 20:39
updated: 2022-06-18 20:40
---
---
**Links**: 
- [[103 Golang Index]]
- [[Go - Channels]]

---
## Select
- Select is like a switch but it is only used with channels
- Basic sends and receives on channels are blocking. However, we can use `select` with a `default` clause to implement _non-blocking_ sends and receives.

### Blocking Send and Receive
```go
const (
	logInfo  = "INFO"
	logError = "ERROR"
)

type logEntry struct {
	level   string
	message string
}

func logger(logch <-chan logEntry) {
	for entry := range logch {
		fmt.Printf("[%v] : %v\n", entry.level, entry.message) // this will block until there is something sent to the channel
	}
}

func main() {
	ch := make(chan logEntry)
	go logger(ch)
	ch <- logEntry{level: logError, message: "Error message"} // these statements will block until the value is received
	ch <- logEntry{level: logInfo, message: "Info message"}
}
```
- A  better way of doing it would be using a buffered log channel so that we don't have blocking sends and receives.
- The idea is that you have central logger for logging and it handles the logic for logging.
- Our application shuts down after the last line of main is executed this means out logger is being torn down forcibly.
	- There is no graceful shutdown

### Use case with select
- `select` statement blocks until one of cases is matched
```go
var doneCh = make(chan struct{}) // signal only channel
// 0 memory allocation required in sending the message
// we could have use chan bool but it would have required some memory allocation

func logger(logch <-chan logEntry) {
	for {
		select {
		case entry := <-logch:
			fmt.Printf("[%v] : %v\n", entry.level, entry.message)
		case <-doneCh:
			break
		}
	}
}

func main() {
	ch := make(chan logEntry)
	go logger(ch)
	ch <- logEntry{level: logError, message: "Error message"}
	ch <- logEntry{level: logInfo, message: "Info message"}
	doneCh <- struct{}{} 
}
```
- In the above example we ensure graceful shutdown of the go routine.
	- Previously it was torn down while it was in the for loop.
	- Another way of doing it instead of using the select statement would have been to defer the channel close in main. 

- If we have a `default` case then it no longer becomes a blocking `select` statement.
- Example of non blocking send and receive

```go
messages := make(chan string)
// non blocking receive
select {
    case msg := <-messages:
        fmt.Println("received message", msg)
    default:
        fmt.Println("no message received")
}
// non blocking send
msg := "hi"
    select {
    case messages <- msg:
        fmt.Println("sent message", msg)
    default:
        fmt.Println("no message sent")
}
```
- Here’s a non-blocking receive. If a value is available on `messages` then `select` will take the `<-messages` `case` with that value. If not it will immediately take the `default` case.
- A non-blocking send works similarly. Here `msg` cannot be sent to the `messages` channel, because the channel has no buffer and there is no receiver. Therefore the `default` case is selected.

> [!caution]- If multiple channels receive value simultaneously, behaviour is undefined
> There is no rule like in switch block the first one that matches is executed. 
> Order of statements in select doesn't matter.