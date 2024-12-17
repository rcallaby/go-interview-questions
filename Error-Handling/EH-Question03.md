# Question 03 - Error Handling

## What is the difference between errors.New and fmt.Errorf in Go? When should you use each?

In Go, both `errors.New` and `fmt.Errorf` are used to create errors, but they are suited for different purposes. Let’s examine the differences and scenarios for their usage, along with examples.

---

### **`errors.New`**
- **Purpose**: Use `errors.New` to create a simple, static error with a constant message.
- **Return Type**: It returns an error constructed from the provided string.
- **Use Case**: When you need a simple error message without any formatting or dynamic content.

**Example**:
```go
package main

import (
	"errors"
	"fmt"
)

func simpleErrorExample() error {
	return errors.New("something went wrong")
}

func main() {
	err := simpleErrorExample()
	if err != nil {
		fmt.Println(err) // Output: something went wrong
	}
}
```

---

### **`fmt.Errorf`**
- **Purpose**: Use `fmt.Errorf` to create an error with a formatted message. It supports formatting verbs (e.g., `%s`, `%d`).
- **Return Type**: It returns an error that can include dynamically formatted details.
- **Use Case**: When the error message needs to include variable data or contextual information.

**Example**:
```go
package main

import (
	"fmt"
)

func detailedErrorExample(userID int) error {
	return fmt.Errorf("user with ID %d not found", userID)
}

func main() {
	err := detailedErrorExample(42)
	if err != nil {
		fmt.Println(err) // Output: user with ID 42 not found
	}
}
```

---

### **Differences**

| Feature                  | `errors.New`                         | `fmt.Errorf`                           |
|--------------------------|---------------------------------------|----------------------------------------|
| **Purpose**              | Simple, static errors                | Errors with dynamic or formatted messages |
| **Complexity**           | Lightweight                         | Supports rich formatting capabilities  |
| **Use Case**             | Constant/static error messages       | Dynamic, contextual error messages     |
| **Error Message Content**| Fixed string                        | Supports format verbs and dynamic values |
| **Dependency**           | Requires only the `errors` package   | Requires the `fmt` package             |

---

### **When to Use Each**

1. **Use `errors.New`**:
   - When the error message is static and simple.
   - To avoid unnecessary overhead if formatting isn't required.
   - Example:
     ```go
     var ErrInvalidInput = errors.New("invalid input provided")
     ```

2. **Use `fmt.Errorf`**:
   - When the error message needs to include variable information.
   - For creating detailed and contextual error messages.
   - When combining errors using the `%w` verb for error wrapping (introduced in Go 1.13).

   **Example of Wrapping Errors**:
   ```go
   package main

   import (
   	"errors"
   	"fmt"
   )

   var ErrNotFound = errors.New("item not found")

   func wrappedErrorExample(id int) error {
   	return fmt.Errorf("failed to fetch item %d: %w", id, ErrNotFound)
   }

   func main() {
   	err := wrappedErrorExample(101)
   	if err != nil {
   		fmt.Println(err) // Output: failed to fetch item 101: item not found

   		// Check if the error wraps ErrNotFound
   		if errors.Is(err, ErrNotFound) {
   			fmt.Println("Underlying error: item not found")
   		}
   	}
   }
   ```

   Here, `fmt.Errorf` wraps the underlying error (`ErrNotFound`) and adds context, making it easy to propagate errors while preserving the root cause.

---

### **Best Practices**
- Prefer **`errors.New`** for predefined constants or static error messages (e.g., package-level errors).
- Use **`fmt.Errorf`** for errors that require dynamic content, context, or wrapping of existing errors.
- Combine both in appropriate scenarios:
  ```go
  var ErrDatabase = errors.New("database error")

  func fetchUser(id int) error {
  	return fmt.Errorf("fetchUser failed: %w", ErrDatabase)
  }
  ```