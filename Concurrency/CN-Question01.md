# Question 01 - Concurrency

## What is a race condition in Go? How would you detect and fix race conditions in a Go program?

A **race condition** in Go occurs when two or more goroutines access shared memory (a variable or resource) concurrently, and at least one of the accesses is a write operation. This can lead to unpredictable and incorrect behavior, as the outcome depends on the timing and interleaving of the goroutines' execution.

### Detecting Race Conditions in Go

Go has built-in tooling to help detect race conditions:
- Use the `-race` flag when running or testing your Go program. This enables the Go race detector, which analyzes goroutines' accesses to shared variables at runtime.

### Example: Race Condition in Go

Here’s a simple program that has a race condition:

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var counter int
	var wg sync.WaitGroup

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			counter++ // Race condition: multiple goroutines writing to `counter`
		}()
	}

	wg.Wait()
	fmt.Println("Counter:", counter)
}
```

#### Explanation:
- Multiple goroutines are incrementing `counter` without proper synchronization.
- The final value of `counter` is unpredictable and can vary between runs.

#### Detect the Race:
Run the program with the `-race` flag:
```sh
go run -race main.go
```

Output (example):
```
WARNING: DATA RACE
Write at 0x00c0000b4010 by goroutine 6:
  main.main.func1()

Previous write at 0x00c0000b4010 by goroutine 5:
  main.main.func1()
```

This output indicates a race condition was detected.

---

### Fixing Race Conditions

There are multiple ways to fix race conditions in Go:

#### 1. **Using a Mutex**
A `sync.Mutex` ensures that only one goroutine accesses the shared resource at a time.

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var counter int
	var mu sync.Mutex
	var wg sync.WaitGroup

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			mu.Lock()   // Lock the mutex before accessing `counter`
			counter++
			mu.Unlock() // Unlock the mutex after accessing `counter`
		}()
	}

	wg.Wait()
	fmt.Println("Counter:", counter)
}
```

Here, the `mu.Lock()` and `mu.Unlock()` calls ensure that only one goroutine can increment `counter` at a time, preventing race conditions.

---

#### 2. **Using Channels**
Channels can be used to synchronize goroutines and safely share data.

```go
package main

import (
	"fmt"
)

func main() {
	counter := 0
	ch := make(chan int)

	// Goroutine to increment counter
	go func() {
		for i := 0; i < 10; i++ {
			ch <- 1
		}
		close(ch)
	}()

	// Main goroutine to sum values from the channel
	for val := range ch {
		counter += val
	}

	fmt.Println("Counter:", counter)
}
```

In this example:
- The channel `ch` serializes access to `counter`.
- The main goroutine reads values from the channel and updates `counter`.

---

#### 3. **Using `sync/atomic`**
The `sync/atomic` package provides low-level atomic operations for managing shared variables without a mutex.

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	var counter int64 // Use int64 for atomic operations
	var wg sync.WaitGroup

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			atomic.AddInt64(&counter, 1) // Atomically increment `counter`
		}()
	}

	wg.Wait()
	fmt.Println("Counter:", counter)
}
```

Here:
- `atomic.AddInt64` ensures that the increment operation is atomic, preventing race conditions.

---

### Comparison of Solutions

| Method           | Advantages                                      | Disadvantages                                   |
|-------------------|------------------------------------------------|------------------------------------------------|
| **Mutex**         | Simple to implement, clear intent              | May block goroutines and cause performance issues in highly concurrent programs. |
| **Channels**      | Idiomatic in Go, encourages goroutine communication | May introduce complexity, can be slower due to context switching. |
| **Atomic**        | Fast and lightweight                           | Limited to basic operations (e.g., increment, compare-and-swap). |

---

### Best Practices to Avoid Race Conditions

1. **Immutable Data**: Share data between goroutines as immutable whenever possible.
2. **Proper Synchronization**: Use synchronization primitives like `sync.Mutex` or `sync/atomic` for shared variables.
3. **Minimize Shared State**: Design your program to minimize the need for shared state.
4. **Test with `-race`**: Always test concurrent code with the `-race` flag during development.

By using these techniques and tools, you can detect and fix race conditions in Go programs effectively.


