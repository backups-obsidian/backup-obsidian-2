---
created: 2022-05-29 16:01
updated: 2022-06-11 11:47
---
---
**Links**: [[103 Golang Index]]

---
## Data Race
- When 2 go routines are executing concurrently (at the same time) if there is no special handling then it can lead to race conditions.
	- These are very difficult to debug.
- Race conditions occur because of **unsynchronised access to memory**. 
- Example of a race condition
```go
import (
	"fmt"
	"sync"
)

func main() {
	var wg sync.WaitGroup
	gr := 100
	wg.Add(gr * 2)
	value := 0

	// we are not using wg pointers here since there is nothing to pass
	// we use pointers in functions because in go functions are call by value
	for i := 0; i < gr; i++ {
		go func() {
			defer wg.Done()
			value++
		}()
		go func() {
			defer wg.Done()
			value--
		}()
	}

	wg.Wait()
	fmt.Println(value)
}
// value can be anything
// Value of n depends on which go routine finishes first
```

- We can use built in race detector to find race conditions in go.
	- To use it we pass it as a flag when running main.go : `go run -race main.go`

## Mutexes
- Mutex comes from mutual exclusion
- This is called **explicit synchronisation** (from experience we realise this variable can be subjected to race condition) where variable access is protected through synchronisation primitives such as a mutex.
- Solving the above race condition using a mutex. This problem could also have been solved by a channel
```go
import (
	"fmt"
	"sync"
)

func main() {
	var m sync.Mutex
	var wg sync.WaitGroup
	gr := 100
	wg.Add(gr * 2)
	value := 0

	// if we were using named functions then we would have to pass pointers to the mutex
	for i := 0; i < gr; i++ {
		go func() {
			defer wg.Done()
			m.Lock()
			value++
			m.Unlock()
		}()
		go func() {
			defer wg.Done()
			m.Lock()
			value--
			m.Unlock()
		}()
	}

	wg.Wait()
	fmt.Println(value)
	// even though we have used mutexes if fmt.Println() was above wg.Wait() then
	// it would have been a race condition
}
```