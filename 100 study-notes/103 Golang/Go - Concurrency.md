---
created: 2022-05-29 16:01
updated: 2022-06-11 11:00
---
---
**Links**: [[103 Golang Index]]

---
## Concurrency vs Parallelism
- Concurrency means loading more goroutines at a time. If one goroutine blocks, another one is picked up and started. *On single core CPU you can run ONLY concurrent applications but they are not run in parallel*.
- **Parallelism means multiple goroutines executed at the same time**. 
	- It requires *multiple CPUs*.
- *Concurrency means independently executing processes* or dealing with multiple things at once, **while parallelism is the simultaneous execution of processes** and require multiple core CPUs.
- **Concurrency means context switching**.
	- CPU time is divided between different processes.

## Goroutines
- A goroutine is a **lightweight thread of execution** 
	- Goroutines are key ingredients to achieve concurrency in Go.
- A **goroutine is a function** that is capable of running concurrently with other functions. 
- To create a goroutine we use the *keyword* `go` *followed by a function invocation*
- Goroutines are *far smaller that threads*, they *typically take around 2kB* of stack space to initialise compared to a *thread* which takes a fixed size of *1-2Mb*.
- An *OS Thread Stack is fixed size* but a **goroutine stack size shrinks and grows as needed**.
- *OS threads are scheduled by the OS kernel*, but *goroutines are scheduled by its own Go Scheduler* using a technique called **`m:n` scheduling**, because it multiplexes (or schedules) *m goroutines on n OS threads*.
	- Scheduling a goroutine is much cheaper than scheduling a thread.
- Goroutines have **no identity**. There is no notion of identity that is accessible to the programmer.

### Miscellaneous
- We can explicitly control the number of threads (logical CPUs) that will be used using `runtime.GOMAXPROCS(0)`
	- This is the n in `m:n` scheduling
	- If argument is < 1 then it doesn't change the setting
	- It is by default equal to the number of logical cpu cores `runtime.NumCPU()`

## WaitGroups
- By default `main` doesn't wait for other go routines to finish. 
	- We need some sort of synchronisation.
```go
import (
	"fmt"
)

func f2() {
	fmt.Println("function 2")
}

func main() {
	fmt.Println("hello there")
	go f2()
	fmt.Println("last line of main")
}
// hello there
// last line of main
```
- In the above example `f2` didn't get time to execute and `main` didn't wait for it. 
	- If we would have added a delay then `f2` would have got time to execute but this is not ideal.
- This synchronisation problem is solved by `waitgroups`
	- It *will wait of all the go routines that have been launched to finish*.
- The pattern to use `sync.WaitGroup` is:
	- Create a new variables of a `sync.WaitGroup` (wg)
	- Call `wg.Add(n)` where `n` is the number of goroutines to wait for
	- Execute `defer wg.Done()` in each goroutine to indicate to the `WaitGroup` that the goroutine has finished executing
	- Call `wg.Wait()` in main() **where we want to block**.
```go
import (
	"fmt"
	"sync"
)

func f2(wg *sync.WaitGroup) {
	defer (*wg).Done() // same as wg.Done()
	fmt.Println("function 2")
}

func main() {
	var wg sync.WaitGroup
	wg.Add(1) // no of goroutines

	fmt.Println("hello there")
	go f2(&wg) // takes a pointer to wg

	wg.Wait()
	fmt.Println("last line of main")
}
// hello there
// function 2
// last line of main
```

### Example 
```go
import (
	"fmt"
	"log"
	"net/http"
	"runtime"
	"strings"
	"sync"
)

func checkAndSaveBody(url string, wg *sync.WaitGroup) {
	resp, err := http.Get(url)
	if err != nil {
		fmt.Printf("%s is DOWN!\n", url)
	} else {
		fmt.Printf("Status Code: %d  ", resp.StatusCode)
	}
	wg.Done()
}

func main() {

	urls := []string{"https://www.golang.org", "https://www.google1.com", "https://www.medium.com"}

	var wg sync.WaitGroup
	wg.Add(len(urls)) //  n is len(urls) - the number of goroutines to wait for

	// this would be very fast if we had thousands of urls to check
	for _, url := range urls {
		go checkAndSaveBody(url, &wg)
	}

	fmt.Println("No. of Goroutines:", runtime.NumGoroutine())
	wg.Wait()
}

//** EXPECTED OUTPUT: **//
// No. of Goroutines: 4
// https://www.google1.com is DOWN!
// Status Code: 200  Writing response Body to www.golang.org.txt
// Status Code: 200  Writing response Body to www.medium.com.txt
```