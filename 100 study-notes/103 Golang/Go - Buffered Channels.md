---
created: 2022-06-18 20:39
updated: 2022-06-19 08:41
---
---
**Links**: 

- [[103 Golang Index]]
- [[Go - Channels]]

---
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
