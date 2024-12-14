# Question 03 - Design Patterns

## How would you implement a thread-safe Singleton in Go? Can you explain your code step by step and why each part is necessary?

Here’s how you can implement a **thread-safe Singleton pattern** in Go. Go's `sync` package makes it straightforward to ensure thread safety with the `sync.Once` type. Below is a concise implementation of a Singleton and an explanation of every part:

### Code:

```go
package main

import (
	"fmt"
	"sync"
)

// Singleton structure
type Singleton struct {
	Value string
}

var (
	instance *Singleton
	once     sync.Once // Ensures Singleton initialization happens only once
)

// GetInstance returns the Singleton instance
func GetInstance() *Singleton {
	once.Do(func() {
		// This block is executed only once, even in concurrent access
		instance = &Singleton{Value: "Initialized Singleton"}
	})
	return instance
}

func main() {
	// Test Singleton
	s1 := GetInstance()
	fmt.Println(s1.Value)

	// Modify Singleton value
	s1.Value = "Updated Singleton"

	s2 := GetInstance()
	fmt.Println(s2.Value) // Should print "Updated Singleton", showing it's the same instance
}
```

---

### Step-by-Step Explanation:

#### **1. Define the `Singleton` structure**
The `Singleton` type holds any data or behavior you want to encapsulate in a single instance:

```go
type Singleton struct {
	Value string
}
```

---

#### **2. Declare global variables**
Two global variables are defined to manage the Singleton:
- `instance` (a pointer to the Singleton): This will hold the actual instance of the Singleton.
- `once` (an instance of `sync.Once`): This ensures that the initialization code runs exactly **once**, regardless of how many goroutines try to access it simultaneously.

```go
var (
	instance *Singleton
	once     sync.Once
)
```

The use of `sync.Once` ensures thread safety without requiring explicit locks like `sync.Mutex`.

---

#### **3. `GetInstance` method**
This method provides global access to the Singleton instance. It ensures the instance is initialized only once, even if multiple goroutines call `GetInstance` simultaneously.

```go
func GetInstance() *Singleton {
	once.Do(func() {
		instance = &Singleton{Value: "Initialized Singleton"}
	})
	return instance
}
```

##### **Key Points about `sync.Once`:**
- `once.Do()` guarantees that the initialization logic inside the anonymous function runs only once. If multiple goroutines call `GetInstance` simultaneously, only one will execute the `func()`.
- Subsequent calls to `once.Do` do nothing, ensuring that the Singleton is only initialized once.

---

#### **4. `main` function for testing**
Here’s how you can verify the Singleton:
- Call `GetInstance` multiple times and check if the returned instance is always the same.
- Update the `Singleton`'s `Value` in one instance and verify that the change is reflected across all instances (since they point to the same memory).

```go
func main() {
	s1 := GetInstance()
	fmt.Println(s1.Value) // Prints: Initialized Singleton

	s1.Value = "Updated Singleton"

	s2 := GetInstance()
	fmt.Println(s2.Value) // Prints: Updated Singleton
}
```

---

### Why This Implementation Works:
1. **Thread Safety**: `sync.Once` internally uses atomic operations and memory barriers to ensure that the initialization code executes exactly once across all goroutines.
2. **Lazy Initialization**: The `instance` is not created until `GetInstance` is called for the first time. This saves resources if the Singleton is never used.
3. **Global Access**: The Singleton instance is accessible globally through `GetInstance`.

---

### When and Why to Use This Pattern
Use a thread-safe Singleton when:
- You need exactly one instance of a type across the application.
- Concurrent access to the instance is expected.

Examples:
- Database connection pools.
- Configuration managers.
- Logging systems.

This implementation is idiomatic in Go, leveraging the standard library’s concurrency primitives for both clarity and performance.