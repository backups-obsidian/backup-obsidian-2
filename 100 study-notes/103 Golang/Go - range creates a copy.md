---
created: 2022-06-05 11:28
updated: 2022-07-01 18:58
---
---
**Links**: [[103 Golang Index]]

---
## `range` creates a copy
- `range` is used to iterate over over slices, maps, arrays and channels.
- A lesser known fact of `range` is that it returns a copy of the value while iterating.

### Example 1
```go
package main

import "fmt"

type MyType struct {
	field int
}

func main() {
	var array [3]MyType

	for _, e := range array {
		e.field = 2
	}

	for _, e := range array {
		fmt.Printf("%d ", e.field)
	}
}
// 0 0 0
```

- On the other hand if tweak the first for loop a little we can record the changes

```go
for i := range array {
	array[i].field = 2
}
// or 
for i := range array {
	e := &array[i]
	e.field = 2
}
```

### Example 2 - Observing the difference with storage addresses

```go
x := make([]int, 3)

x[0], x[1], x[2] = 1, 2, 3

for i, val := range x {
    println(&x[i], "vs.", &val)
}
// The code prints you completely different memory locations for the value from range and the actual value in the slice:

// 0xf84000f010 vs. 0x7f095ed0bf68
// 0xf84000f014 vs. 0x7f095ed0bf68
// 0xf84000f018 vs. 0x7f095ed0bf68
```

### Example 3
```go
package main

import "fmt"

type Record struct {
    Id int
    Name string
}

var records = []Record{
    Record{1, "First"},
    Record{2, "Second"},
    Record{3, "Third"},
}

func findRecod(id int) (foundRecord *Record) {
    for _, record := range records {
        if record.Id == id {
            foundRecord = &record
        }       
        // ...something more here
    }
    return foundRecord
}

func main() {
    foundRecord := findRecod(2)
    if foundRecord == nil {
        fmt.Println("Nothing found")
    } else {
        fmt.Println("Found: ", foundRecord.Name)
    }
}

// Output
// Found Third

// Solution
// foundRecord = &records[i]
```

> [!caution]- You are **returning pointer** to a `record` variable which is **_reused_ by each iteration of the loop**. **Last iteration sets the pointer to the third structure**.
> The reuse of variable has an enormous advantage. It does not allocate memory in every iteration of the loop. This saves a lot of garbage collection time.
>
> This can be verified by adding the following under the if block in the for loop `fmt.Printf("%p, %p\n", &record, &records[i])`