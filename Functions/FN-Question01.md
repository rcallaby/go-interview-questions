# Question 01 - Functions

##  How can an anonymous function with a receiver be defined and invoked? 

In Go, anonymous functions **cannot** be directly used as method receivers. Go requires method receivers to be explicitly defined on named types (e.g., struct or a named type alias). You cannot define an anonymous function and assign it as a method of a type. This is a limitation of Go's design and syntax.

However, you can define an anonymous function **within a method** of a type and invoke it there. Below, I'll demonstrate how to use an anonymous function inside a method of a named type:

### Example: Anonymous Function Within a Method

```go
package main

import "fmt"

// A named type (struct)
type Person struct {
    Name string
}

// Define a method on the named type
func (p Person) Greet() {
    // Define an anonymous function within the method
    anonymousFunc := func(greeting string) {
        fmt.Printf("%s, %s!\n", greeting, p.Name)
    }

    // Invoke the anonymous function
    anonymousFunc("Hello")
}

func main() {
    // Create an instance of the type
    john := Person{Name: "John"}

    // Call the method, which internally invokes the anonymous function
    john.Greet()
}
```

### Explanation:
1. **Named Type**: The `Person` struct is a named type.
2. **Method with Receiver**: The `Greet` method has a receiver `(p Person)` that allows it to be associated with the `Person` type.
3. **Anonymous Function**: Inside the `Greet` method, an anonymous function is defined and stored in the variable `anonymousFunc`.
4. **Invocation**: The anonymous function is called within the `Greet` method.

This is the correct way to use anonymous functions in Go in the context of methods.

### Why Can't Anonymous Functions Be Receivers?
Go's syntax requires method receivers to be defined on named types explicitly. This design enforces a level of clarity and consistency in associating methods with types and prevents ambiguity that could arise from dynamically assigning methods to anonymous functions.