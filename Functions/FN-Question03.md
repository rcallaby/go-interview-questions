# Question 03 - Functions

## How does Go handle variadic functions, and what are some best practices when using them? 

Go handles **variadic functions** by allowing you to pass a variable number of arguments to a function. This is done by using `...` syntax in the function parameter list. A variadic function takes zero or more arguments of the specified type. Inside the function, the arguments are received as a slice.

Here’s a detailed explanation with **examples** and **best practices**:

---

### **1. Declaring and Using a Variadic Function**

You declare a variadic function by appending `...` before the type of the final parameter. For example:

```go
package main

import "fmt"

func sum(numbers ...int) int {
    total := 0
    for _, number := range numbers {
        total += number
    }
    return total
}

func main() {
    fmt.Println(sum(1, 2, 3))          // Output: 6
    fmt.Println(sum(10, 20, 30, 40))  // Output: 100
    fmt.Println(sum())                // Output: 0 (no arguments passed)
}
```

- In the above example, `numbers` is a slice of `int`.
- If no arguments are passed, `numbers` will be an empty slice (`nil`).

---

### **2. Passing a Slice to a Variadic Function**

If you already have a slice, you can pass it to a variadic function by using the `...` syntax:

```go
package main

import "fmt"

func sum(numbers ...int) int {
    total := 0
    for _, number := range numbers {
        total += number
    }
    return total
}

func main() {
    nums := []int{5, 10, 15}
    fmt.Println(sum(nums...))  // Output: 30
}
```

- Using `nums...` expands the slice into individual arguments.

---

### **3. Combining Regular and Variadic Parameters**

A variadic parameter must always be the **last parameter** in the function definition:

```go
package main

import "fmt"

func greet(prefix string, names ...string) {
    for _, name := range names {
        fmt.Printf("%s %s\n", prefix, name)
    }
}

func main() {
    greet("Hello", "Alice", "Bob", "Charlie")
    // Output:
    // Hello Alice
    // Hello Bob
    // Hello Charlie
}
```

---

### **4. Best Practices for Using Variadic Functions**

#### **a. Be Mindful of Argument Type**
All variadic arguments must be of the same type. If you need to support multiple types, consider using **interfaces**:

```go
package main

import "fmt"

func printAll(items ...interface{}) {
    for _, item := range items {
        fmt.Println(item)
    }
}

func main() {
    printAll(42, "Hello", 3.14, true)
    // Output:
    // 42
    // Hello
    // 3.14
    // true
}
```

- Using `interface{}` allows handling multiple types, but you lose type safety and need to use type assertions.

---

#### **b. Avoid Overuse**
While variadic functions can make some APIs flexible, overusing them can reduce clarity. For instance, instead of accepting a variadic list of configuration options, consider passing a structured configuration object.

#### **c. Handle Nil Slices Gracefully**
Ensure your variadic function works even when no arguments are passed (an empty slice). For example:

```go
package main

import "fmt"

func average(numbers ...float64) float64 {
    if len(numbers) == 0 {
        return 0 // Handle empty input
    }

    total := 0.0
    for _, number := range numbers {
        total += number
    }
    return total / float64(len(numbers))
}

func main() {
    fmt.Println(average())             // Output: 0
    fmt.Println(average(1.5, 2.5, 3))  // Output: 2
}
```

---

#### **d. Consider Performance Implications**
Since variadic arguments are converted into a slice, they incur allocation overhead. If performance is critical and the number of arguments is small and fixed, consider using explicit parameters instead.

```go
package main

import "fmt"

// Explicit version for performance
func addThree(a, b, c int) int {
    return a + b + c
}

func main() {
    fmt.Println(addThree(1, 2, 3))  // Output: 6
}
```

---

#### **e. Document the Expected Usage**
If a variadic function expects specific behavior (e.g., at least one argument), document it clearly or enforce it with checks:

```go
package main

import (
    "errors"
    "fmt"
)

func min(numbers ...int) (int, error) {
    if len(numbers) == 0 {
        return 0, errors.New("min requires at least one argument")
    }

    minValue := numbers[0]
    for _, number := range numbers[1:] {
        if number < minValue {
            minValue = number
        }
    }
    return minValue, nil
}

func main() {
    result, err := min()
    if err != nil {
        fmt.Println(err)  // Output: min requires at least one argument
    }

    result, _ = min(8, 3, 5, 7)
    fmt.Println(result)  // Output: 3
}
```

---

### **5. Advanced Example: Logger with Variadic Parameters**

Here’s an example of a flexible logger function using variadic arguments:

```go
package main

import (
    "fmt"
    "time"
)

func log(message string, args ...interface{}) {
    timestamp := time.Now().Format("2006-01-02 15:04:05")
    fmt.Printf("[%s] %s: ", timestamp, message)
    for _, arg := range args {
        fmt.Printf("%v ", arg)
    }
    fmt.Println()
}

func main() {
    log("User logged in", "userID", 1234, "IP", "192.168.1.1")
    // Output (timestamp will vary):
    // [2024-12-15 14:30:45] User logged in: userID 1234 IP 192.168.1.1
}
```

---

### Summary of Best Practices

1. **Be Type-Specific**: Use variadic functions for arguments of the same type. For mixed types, consider `interface{}` but with caution.
2. **Use Explicit Parameters When Possible**: Avoid variadic functions if a fixed number of arguments is more appropriate.
3. **Document Usage**: Clearly define the expected usage and behavior.
4. **Handle Empty Input**: Ensure the function handles an empty argument list gracefully.
5. **Optimize for Performance**: Avoid unnecessary slice allocations in performance-critical code.

Variadic functions are a powerful feature in Go, but careful design and thoughtful usage are essential for creating maintainable and efficient code.