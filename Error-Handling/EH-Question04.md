# Question 04 - Error Handling

## How does Go 1.13+ handle error wrapping, and how is it different from older versions? 

In Go 1.13 and later, **error wrapping** was introduced as part of the `errors` package. This feature allows errors to be wrapped with additional context while still allowing the original error to be inspected or unwrapped. This is a significant improvement over earlier versions of Go, where error wrapping was typically handled by third-party libraries or custom implementations.

Here’s an **expert explanation** of how error wrapping works in Go 1.13+ and how it differs from older versions.

---

### **Error Wrapping in Go 1.13+**
Go 1.13 introduced two main functions in the `errors` package for error wrapping:

1. **`errors.Wrap`:** Not directly available. Instead, use `fmt.Errorf` with the `%w` verb to wrap an error.
2. **`errors.Unwrap`:** Used to retrieve the original error from a wrapped error.
3. **`errors.Is`:** Checks if an error matches a target error, including wrapped errors.
4. **`errors.As`:** Checks if an error or a wrapped error matches a specific type.

#### **Code Example: Wrapping an Error**
```go
package main

import (
	"errors"
	"fmt"
)

func someFunction() error {
	return errors.New("original error")
}

func anotherFunction() error {
	err := someFunction()
	if err != nil {
		// Wrap the error with additional context
		return fmt.Errorf("anotherFunction failed: %w", err)
	}
	return nil
}

func main() {
	err := anotherFunction()
	if err != nil {
		fmt.Println("Error:", err)

		// Unwrap the error
		unwrapped := errors.Unwrap(err)
		fmt.Println("Unwrapped Error:", unwrapped)

		// Check if the error matches the original error
		if errors.Is(err, errors.New("original error")) {
			fmt.Println("The error matches the original error")
		} else {
			fmt.Println("The error does not match")
		}
	}
}
```

#### **Output:**
```
Error: anotherFunction failed: original error
Unwrapped Error: original error
The error does not match
```

---

#### **Key Points:**
- The `%w` verb in `fmt.Errorf` is used for wrapping errors.
- `errors.Unwrap` retrieves the wrapped error.
- `errors.Is` checks if an error or its wrapped chain matches a target error.

---

### **Differences from Older Versions**
Before Go 1.13, there was no standard way to wrap errors. Developers typically used third-party libraries like [pkg/errors](https://github.com/pkg/errors) to wrap errors or implemented custom solutions.

#### **Custom Wrapping in Go <1.13**
In older versions of Go, you might have written something like this:

```go
package main

import (
	"fmt"
)

// Custom error type to wrap another error
type MyError struct {
	msg string
	err error
}

func (e *MyError) Error() string {
	return fmt.Sprintf("%s: %v", e.msg, e.err)
}

func (e *MyError) Unwrap() error {
	return e.err
}

func someFunction() error {
	return fmt.Errorf("original error")
}

func anotherFunction() error {
	err := someFunction()
	if err != nil {
		// Manually wrap the error
		return &MyError{
			msg: "anotherFunction failed",
			err: err,
		}
	}
	return nil
}

func main() {
	err := anotherFunction()
	if err != nil {
		fmt.Println("Error:", err)

		// Unwrap manually using custom implementation
		if customErr, ok := err.(*MyError); ok {
			fmt.Println("Unwrapped Error:", customErr.Unwrap())
		}
	}
}
```

#### **Drawbacks of Older Versions:**
- Wrapping required a custom error type or third-party libraries like `pkg/errors`.
- Unwrapping was not standardized (`Unwrap` method was not part of the `error` interface).
- Matching errors (`errors.Is`) and extracting specific types (`errors.As`) were harder and less idiomatic.

---

### **Summary of Differences**
| Feature                | Go 1.13+                                | Pre-Go 1.13                     |
|------------------------|-----------------------------------------|---------------------------------|
| **Error Wrapping**     | `fmt.Errorf("context: %w", err)`         | Custom types or libraries like `pkg/errors` |
| **Unwrapping**         | `errors.Unwrap(err)`                    | Manually implemented `Unwrap` method |
| **Error Matching**     | `errors.Is(err, targetErr)`             | Manual comparison (`==`) or custom logic |
| **Type Assertion**     | `errors.As(err, &targetType)`           | Type assertion (`err.(*Type)`) |

---

### **Practical Tips**
1. **Always use `fmt.Errorf` with `%w` for wrapping in Go 1.13+.**
2. **Use `errors.Is` for comparison instead of manually comparing strings or error instances.**
3. **Use `errors.As` for extracting specific error types.**

These improvements make error handling in Go 1.13+ much more consistent and ergonomic than in earlier versions.