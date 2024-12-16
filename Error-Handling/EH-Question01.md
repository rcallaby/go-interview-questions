# Question 01 - Error Handling

##  What is the purpose of the errors.Is and errors.As functions introduced in Go 1.13?

The `errors.Is` and `errors.As` functions introduced in Go 1.13 provide a powerful way to work with and inspect errors, making error handling more robust and readable. These functions are particularly useful when dealing with wrapped errors using the `fmt.Errorf` function with `%w` or with custom error types that implement the `Unwrap` method.

---

### Purpose of `errors.Is`

The `errors.Is` function checks whether a specific error exists in an error chain. This is useful for comparing errors, especially when errors are wrapped.

---

#### Example: Using `errors.Is`

```go
package main

import (
	"errors"
	"fmt"
)

var ErrNotFound = errors.New("not found")

func main() {
	// Wrapping the error
	wrappedErr := fmt.Errorf("database query failed: %w", ErrNotFound)

	// Check if wrappedErr contains ErrNotFound
	if errors.Is(wrappedErr, ErrNotFound) {
		fmt.Println("Error is 'not found'")
	} else {
		fmt.Println("Error is not 'not found'")
	}
}
```

**Output:**
```
Error is 'not found'
```

**Explanation:**
- `errors.Is` traverses the error chain to see if `ErrNotFound` is part of it.
- This is extremely useful for error handling when you want to check for specific known error types.

---

### Purpose of `errors.As`

The `errors.As` function allows you to check whether an error is of a specific type (or implements a specific interface) in an error chain and retrieves that error.

---

#### Example: Using `errors.As` with Custom Error Types

```go
package main

import (
	"errors"
	"fmt"
)

// CustomError defines a custom error type
type CustomError struct {
	Code int
	Msg  string
}

func (e *CustomError) Error() string {
	return fmt.Sprintf("Code %d: %s", e.Code, e.Msg)
}

func main() {
	// Create a CustomError and wrap it
	err := fmt.Errorf("operation failed: %w", &CustomError{Code: 404, Msg: "resource not found"})

	var customErr *CustomError
	// Check if the error is of type *CustomError and extract it
	if errors.As(err, &customErr) {
		fmt.Printf("Custom error detected: Code %d, Msg: %s\n", customErr.Code, customErr.Msg)
	} else {
		fmt.Println("Error is not of type *CustomError")
	}
}
```

**Output:**
```
Custom error detected: Code 404, Msg: resource not found
```

**Explanation:**
- `errors.As` traverses the error chain to find the first error that matches the specified type.
- In this case, it identifies the `*CustomError` type, allowing access to its fields (`Code` and `Msg`).

---

### How `errors.Is` and `errors.As` Work Together

Both functions traverse the error chain using the `Unwrap` method, which is implemented by Go's standard library error-wrapping functions (like `fmt.Errorf("%w")`) or custom implementations.

---

#### Example: Combining `errors.Is` and `errors.As`

```go
package main

import (
	"errors"
	"fmt"
)

// CustomError defines a custom error type
type CustomError struct {
	Code int
	Msg  string
}

func (e *CustomError) Error() string {
	return fmt.Sprintf("Code %d: %s", e.Code, e.Msg)
}

var ErrUnauthorized = errors.New("unauthorized access")

func main() {
	// Create and wrap multiple errors
	baseErr := &CustomError{Code: 403, Msg: "forbidden"}
	wrappedErr := fmt.Errorf("API call failed: %w", baseErr)
	finalErr := fmt.Errorf("request terminated: %w", wrappedErr)

	// Check for a specific error using errors.Is
	if errors.Is(finalErr, baseErr) {
		fmt.Println("Error contains 'forbidden'")
	}

	// Check for a specific type of error using errors.As
	var customErr *CustomError
	if errors.As(finalErr, &customErr) {
		fmt.Printf("Custom error detected: Code %d, Msg: %s\n", customErr.Code, customErr.Msg)
	} else {
		fmt.Println("Error is not of type *CustomError")
	}
}
```

**Output:**
```
Error contains 'forbidden'
Custom error detected: Code 403, Msg: forbidden
```

---

### Key Notes
1. **`errors.Is`:** Use it to compare errors (especially constant or sentinel errors like `ErrNotFound`).
2. **`errors.As`:** Use it to extract or inspect custom error types or interfaces.
3. **Chaining:** Both functions traverse wrapped errors using `Unwrap`, allowing inspection of deeply nested errors.

These functions significantly enhance error handling in Go by making it easier to check and process errors in a structured way.