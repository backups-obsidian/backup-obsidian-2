---
created: 2022-06-05 11:28
updated: 2022-06-18 20:40
---
---
**Links**: [[103 Golang Index]]

---
## Channels
- A channel in go provides a **connection between 2 go routines** allowing them to communicate.
- Channels are designed to *synchronise communication between 2 go routines*.
- Multiple go routines and send and receive values from a single channel.
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

- We can make a *bidirectional channel*, pass it to a function but *use it only for sending or receiving values in a function*.
	- We can say at runtime the bidirectional channel is cast to a unidirectional channel at runtime.
	- This type of casting is specific to channels.

```go
func main () {
	ch := make (chan int)
	wg.Add (2)

	// only receiving function
	go func (ch <-chan int) {
		i := <- ch
		fmt.Println(i)
		wg.Done()
	}(ch)

	// sending function
	go func (ch chan<- int) {
		ch <- 42
		wg.Done ()
	}(ch)
	wg.Wait()
}
```

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

- If you close a channel and then try to send value to the closed channel then your application will panic.

### Only one message in a channel
- By default we work with unbuffered channels which means there can only be one message in a channel. 
	- If we **try to send more messages in the channel we will receive a deadlock error**.

```go
var wg = sync.WaitGroup{}

func main() {
	wg.Add(2)
	ch := make(chan int)
	go func(ch <-chan int) {
		defer wg.Done()
		i := <-ch
		fmt.Println(i)
	}(ch)
	go func(ch chan<- int) {
		defer wg.Done()
		ch <- 45
		ch <- 34 // it will error out here since this go routine will be blocked at this line since there is nothing to receive it. This go routing can never complete
	}(ch)
	wg.Wait()
}
// 45
// error 
```

- One way of avoiding this problem is using the buffered channel. But our message 34 will be lost since this isn't the problem that buffered channels are intended to solve.
	- Buffered channels are meant to be used when the sender and receiver operate at different rates.
- The best way of handling the above problem is using a `for range` loop.
```go
go func(ch <-chan int) {
	defer wg.Done()
	for i := range ch {
		fmt.Println(i)
	} 
}(ch)
// here we are deadlocking in the for loop since we are waiting to receive data
// to over come this we must call close on channel on the sender side,
// This will let for know to stop iterating
```

- This is what the `for range` loop does under the hood.
	- It uses the comma ok syntax under the hood.
```go
for { // infinite loop
	if i, ok := <- ch; ok {
		fmt. Println(i)
	} else {
		break
	}
}
```

- Full working code 
```go
var wg = sync.WaitGroup{}

func main() {
	ch := make(chan int)
	wg.Add(2)

	go func(ch <-chan int) {
		defer wg.Done()
		for i := range ch {
			fmt.Println(i) // looping since there can be any number of values coming into the channel.
		}
	}(ch)

	go func(ch chan<- int) {
		defer wg.Done()
		defer close(ch) // closing the channel after sending
		ch <- 45
		ch <- 34
	}(ch)
	wg.Wait()
}
```

### Another example
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

### Understanding blocking calls
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


## [[Go - Buffered Channels]]
## [[Go - Select]]
