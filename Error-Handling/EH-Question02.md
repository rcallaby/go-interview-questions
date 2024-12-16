# Question 02 - Error Handling

## How can you define and use custom error types in Go? What are the benefits of doing so?

### Defining and Using Custom Error Types in Go

In Go, you can define custom error types by creating types that implement the built-in `error` interface. The `error` interface in Go is very simple and looks like this:

```go
type error interface {
    Error() string
}
```

To create a custom error type, you define a struct or type and implement the `Error()` method for it.

#### Step 1: Defining a Custom Error Type
Here's an example of defining a custom error type:

```go
package main

import (
	"fmt"
)

// Custom error type
type MyError struct {
	Code    int
	Message string
}

// Implement the Error() method for the MyError type
func (e *MyError) Error() string {
	return fmt.Sprintf("Error %d: %s", e.Code, e.Message)
}
```

#### Step 2: Using the Custom Error
You can create an instance of the custom error and use it like any other error:

```go
func riskyOperation(success bool) error {
	if !success {
		return &MyError{
			Code:    123,
			Message: "Something went wrong",
		}
	}
	return nil
}

func main() {
	err := riskyOperation(false)
	if err != nil {
		// Use type assertion to access fields in the custom error
		if myErr, ok := err.(*MyError); ok {
			fmt.Printf("Caught MyError: Code=%d, Message=%s\n", myErr.Code, myErr.Message)
		} else {
			fmt.Println("An error occurred:", err)
		}
	}
}
```

#### Benefits of Custom Error Types
1. **Additional Context**:
   - Custom error types allow you to include extra fields (e.g., error codes, metadata, or structured messages) to provide more context about the error.

2. **Type Assertion**:
   - With custom error types, you can use type assertions to programmatically differentiate between various error types and handle them accordingly.

3. **Better Error Categorization**:
   - By using specific error types, you can categorize and manage errors more effectively, especially in complex applications with multiple error scenarios.

4. **Improved Debugging**:
   - Including additional details in the custom error (like stack traces, operation names, etc.) can make debugging easier.

5. **Extensibility**:
   - Custom error types are extensible, allowing you to evolve your error handling strategy as your application grows.

#### Example: Categorizing Errors
You can define multiple custom error types to represent different categories of errors:

```go
type NotFoundError struct {
	Resource string
}

func (e *NotFoundError) Error() string {
	return fmt.Sprintf("%s not found", e.Resource)
}

type ValidationError struct {
	Field   string
	Message string
}

func (e *ValidationError) Error() string {
	return fmt.Sprintf("Validation failed on %s: %s", e.Field, e.Message)
}

func main() {
	var err error

	// Simulate a NotFoundError
	err = &NotFoundError{Resource: "User"}
	if _, ok := err.(*NotFoundError); ok {
		fmt.Println("Handle not found error:", err)
	}

	// Simulate a ValidationError
	err = &ValidationError{Field: "Email", Message: "Invalid format"}
	if _, ok := err.(*ValidationError); ok {
		fmt.Println("Handle validation error:", err)
	}
}
```

By defining and using custom error types, you can build robust and maintainable error-handling mechanisms in Go.