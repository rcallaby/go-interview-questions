# Question 02 - Functions

## What is a first-class function in Go? Write a program that assigns a function to a variable and passes it as an argument to another function.

In Go, **first-class functions** are functions that can be assigned to variables, passed as arguments to other functions, or returned as values from other functions. This means functions in Go are treated like any other variable.

Here’s a simple Go program that demonstrates assigning a function to a variable and passing it as an argument to another function:

```go
package main

import "fmt"

// A function that takes another function as an argument
func executeFunction(f func(string) string, input string) {
    result := f(input)
    fmt.Println("Result from the passed function:", result)
}

func main() {
    // Assigning a function to a variable
    greet := func(name string) string {
        return "Hello, " + name
    }

    // Passing the function variable as an argument
    executeFunction(greet, "Go Developer")
}
```

### Output:
```
Result from the passed function: Hello, Go Developer
```

### Explanation:
1. **`greet` Function**: A function is assigned to the variable `greet`. This function takes a string as input and returns a greeting message.
2. **`executeFunction`**: This function takes another function (with the signature `func(string) string`) and a string as arguments.
3. The `greet` function is passed as an argument to `executeFunction`, which calls it and prints the result.

This demonstrates Go's support for first-class functions.