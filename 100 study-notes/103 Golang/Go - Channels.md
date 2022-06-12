---
created: 2022-06-05 11:28
updated: 2022-06-11 19:30
---
---
**Links**: [[103 Golang Index]]

---
## Channels
- A channel in go provides a **connection between 2 go routines** allowing them to communicate.
- Channels are used to *communicate in between running go routines*.
- Value of an uninitialised channel is `nil`
```go
var ch chan int
fmt.Println(ch)  // <nil>
// initialising the channel
ch = make(chan int)
fmt.Println(ch) // hexadecimal address (pointer)
```
- Passing channels to functions is same as passing pointers to functions
- Initialisation and declaration
	- `ch := make(chan int)`
- A channel is a 2 way messaging object.
- We send and receive messages using the channel operator  (`<-`)
	- *Sending* value to the channel : `ch <- 10`
	- *Receiving* value from the channel : `num := <- ch`
		- `fmt.Println(<- ch)`
- Closing the channel : `close(ch)`
- The channel created above was a *bidirectional channel*. We can create *uni-directional channels* using : 
	- Only for receiving : `c1 := make(<-chan string)`
	- Only for sending: `c2 := make(chan<- string)`

- Simple example to communicate between 2 go routines.
	- **Remember `main` is also a go routine**
```go
func f1(n int, ch chan int) {
	ch <- n // sending value to the channel
}

func main() {
	ch := make(chan int)
	go f1(10, ch)
	fmt.Println(<-ch) // receiving value from the channel
	close(ch)         // closing the channel
}
```

- Another example
```go
func f1(n int, ch chan int) {
	fact := 1

	for i := 2; i <= n; i++ {
		fact = fact * i
	}
	ch <- fact
}

func main() {
	ch := make(chan int)
	defer close(ch)
	go f1(5, ch)
	fmt.Println(<-ch) // blocking call
	// The main go routine will wait to receive the value from the channel, this is a blocking call.
	// this is the reason why we don't need waitgroups

	// using anonymous functions
	for i := 5; i < 15; i++ {
	    go func(n int, c chan int) {
            f := 1
            for i := 2; i <= n; i++ {
                f *= i
            }
            c <- f
        }(i, ch) // passing values to anonymous functions
        fmt.Printf("Factorial of %d is %d\n", i, <-ch)
    }
}
```
- Understanding blocking calls
```go
func main() {
	c1 := make(chan int) //unbuffered channel

	// Launching a goroutine
	go func(c chan int) {
		fmt.Println("func goroutine starts sending data into the channel")
		c <- 10 // blocking statement, go routine will be stuck here until main reads the value from the channel
		fmt.Println("func goroutine after sending data into the channel")
	}(c1) // calling the anonymous func and passing c1 as argument

	fmt.Println("main goroutine sleeps for 2 seconds")
	time.Sleep(time.Second * 2)

	fmt.Println("main goroutine starts receiving data")
	d := <-c1
	fmt.Println("main goroutine received data:", d)

	// we sleep for a second to give time to the goroutine to finish
	time.Sleep(time.Second)
}

//** EXPECTED OUTPUT: **//
// main goroutine sleeps for 2 seconds
// func goroutine starts sending data into the channel
// main goroutine starts receiving data
// main goroutine received data: 10
// func goroutine after sending data into the channel
```

## Buffered Channels
- The channels create above were unbuffered channels
- If the capacity is non zero then it is a buffered channel
```go
c := make(chan int) // unbuffered channel
c1 := make(chan int, 3) // buffered channel
```
- In the above example the go routine can send 3 values to the channel without blocking.
- We can say communication over unbuffered channels is synchronous. Hence **unbuffered channels are also known as synchronous channels**

> [!note]- The sender of a buffered channel will block only when there is no empty slot in the channel, while the receiver will block on the channel when it's empty.
```go
func main() {
	// Declaring a buffered channel.
	c1 := make(chan int, 3)

	fmt.Println("Channel's capacity:", cap(c1)) // => 3

	// spawning a new goroutine
	go func(c chan int) {
		// sending 5 values into the channel
		for i := 1; i <= 5; i++ {
			fmt.Printf("func goroutine #%d starts sending data into the channel\n", i)
			c <- i
			fmt.Printf("func goroutine #%d after sending data into the channel\n", i)
		}
		// closing the buffered channel.
		close(c)

	}(c1) //calling the anonymous func and passing c1 as argument

	fmt.Println("main goroutine sleeps 2 seconds")
	time.Sleep(time.Second * 2)

	// receiving data from the channel
	for v := range c1 { // v is the value read from the channel, it's like using v := <- c2
		fmt.Println("main goroutine received value from channel:", v)

	}

	// A receive operation on a closed channel will proceed without blocking
	// and yield the zero-value for the type that is sent through the channel.
	fmt.Println(<-c1) // => 0

	// Sending a value into a closed channel will panic.
	// c1 <- 10 // => panic: send on closed channel
}
```

## Select
- Select is like a switch but it is only used with channels
```go
func main() {

	// retrieving the current timestamp in milliseconds
	s := time.Now().UnixNano() / 1000000

	// The `select` statement lets a goroutine wait on multiple communication operations.
	// A select blocks until one of its cases can run, then it executes that case.
	// Select is only used with channels.

	// declaring 2 channels
	c1 := make(chan string)
	c2 := make(chan string)

	// starting the first goroutine using an anonymous function
	go func() {
		time.Sleep(2 * time.Second)

		// sending a message into the channel
		c1 <- "Hello!"
	}()

	// starting the second goroutine using an anonymous function
	go func() {
		time.Sleep(1 * time.Second)

		// sending a message into the channel
		c2 <- "Salut!"
	}()

	// using select to wait on both goroutines
	for i := 0; i < 2; i++ {
		select {
		case msg1 := <-c1:
			fmt.Println("Received", msg1)
		case msg2 := <-c2:
			fmt.Println("Received", msg2)

		}

	}

	e := time.Now().UnixNano() / 1_000_000

	// total execution time is only ~2 seconds since the goroutines executed concurrently.
	fmt.Println(e - s)

	// Basic sends and receives on channels are blocking.
	// However, we can use `select` with a `default` clause to implement non-blocking channels.
}
```