# Question 04 - Functions

## What are the differences between a function and a method in Go?

In Go (Golang), both **functions** and **methods** are ways to encapsulate reusable logic. However, they differ in how they are defined and used. Below is a detailed explanation with examples to clarify the distinctions.

---

### **Key Differences Between Functions and Methods**

| Aspect                | **Function**                                | **Method**                                   |
|-----------------------|---------------------------------------------|---------------------------------------------|
| **Definition**        | Defined without a receiver (standalone).   | Defined with a receiver (attached to a type).|
| **Invocation**        | Called directly by name.                   | Called on an instance of the receiver type. |
| **Association**       | Not associated with any specific type.     | Associated with a specific type (struct, interface, etc.). |
| **Syntax**            | `func funcName(...) {...}`                 | `func (receiver Type) methodName(...) {...}`|
| **Use Case**          | General-purpose, reusable code.            | Behavior or actions tied to a specific type. |

---

### **Code Examples**

#### **1. Function**

A function is standalone and is not tied to any specific type.

```go
package main

import "fmt"

// Function that takes two integers and returns their sum
func add(a int, b int) int {
	return a + b
}

func main() {
	// Call the function directly
	result := add(3, 7)
	fmt.Println("Sum:", result) // Output: Sum: 10
}
```

#### **2. Method**

A method is tied to a specific type, and the receiver specifies which type the method operates on.

```go
package main

import "fmt"

// Define a struct type
type Rectangle struct {
	Width  float64
	Height float64
}

// Method with a value receiver
func (r Rectangle) Area() float64 {
	return r.Width * r.Height
}

// Method with a pointer receiver (modifies the struct)
func (r *Rectangle) Scale(factor float64) {
	r.Width *= factor
	r.Height *= factor
}

func main() {
	rect := Rectangle{Width: 5, Height: 3}

	// Call methods on the Rectangle instance
	fmt.Println("Area:", rect.Area()) // Output: Area: 15

	// Scale modifies the original rectangle (pointer receiver)
	rect.Scale(2)
	fmt.Println("Scaled Area:", rect.Area()) // Output: Scaled Area: 60
}
```

---

### **3. Comparing Functions and Methods**

Here’s an example illustrating when to use a function versus a method:

```go
package main

import "fmt"

// Define a struct for demonstration
type Circle struct {
	Radius float64
}

// Function to calculate the area of a circle
// Not tied to any type
func CircleArea(radius float64) float64 {
	return 3.14 * radius * radius
}

// Method to calculate the area of a circle
// Tied to the Circle type
func (c Circle) Area() float64 {
	return 3.14 * c.Radius * c.Radius
}

func main() {
	// Using the standalone function
	area1 := CircleArea(5)
	fmt.Println("Function Area:", area1) // Output: Function Area: 78.5

	// Using the method
	c := Circle{Radius: 5}
	area2 := c.Area()
	fmt.Println("Method Area:", area2) // Output: Method Area: 78.5
}
```

#### **Key Takeaway**:
- Use **functions** for general-purpose calculations or utilities.
- Use **methods** when you want behavior tied to a specific type.

---

### **4. Value vs Pointer Receivers in Methods**

Another distinction specific to **methods** is the choice between **value receivers** and **pointer receivers**.

#### **Value Receiver**
- A copy of the value is passed to the method.
- Modifications inside the method do not affect the original value.

```go
package main

import "fmt"

type Counter struct {
	Value int
}

// Method with a value receiver
func (c Counter) Increment() {
	c.Value++
}

func main() {
	count := Counter{Value: 10}
	count.Increment()
	fmt.Println("Value:", count.Value) // Output: Value: 10 (unchanged)
}
```

#### **Pointer Receiver**
- A pointer to the value is passed.
- Modifications inside the method affect the original value.

```go
package main

import "fmt"

type Counter struct {
	Value int
}

// Method with a pointer receiver
func (c *Counter) Increment() {
	c.Value++
}

func main() {
	count := Counter{Value: 10}
	count.Increment()
	fmt.Println("Value:", count.Value) // Output: Value: 11 (modified)
}
```

---

### **5. Methods on Non-Struct Types**

Methods in Go can be defined on any type, including custom types or even built-in types.

```go
package main

import "fmt"

// Define a custom type
type MyInt int

// Method on the custom type
func (m MyInt) Double() MyInt {
	return m * 2
}

func main() {
	num := MyInt(10)
	fmt.Println("Double:", num.Double()) // Output: Double: 20
}
```

---

### **6. Practical Use Case**

Using both functions and methods together:

```go
package main

import "fmt"

// Struct to represent a point in 2D space
type Point struct {
	X, Y float64
}

// Function to calculate the distance between two points
func Distance(p1, p2 Point) float64 {
	return ((p2.X - p1.X) * (p2.X - p1.X)) + ((p2.Y - p1.Y) * (p2.Y - p1.Y))
}

// Method to shift a point by a given offset
func (p *Point) Shift(dx, dy float64) {
	p.X += dx
	p.Y += dy
}

func main() {
	p1 := Point{X: 1, Y: 2}
	p2 := Point{X: 4, Y: 6}

	// Use the standalone function
	dist := Distance(p1, p2)
	fmt.Println("Distance:", dist) // Output: Distance: 25 (squared distance)

	// Use the method to shift a point
	p1.Shift(1, 1)
	fmt.Println("Shifted Point:", p1) // Output: Shifted Point: {2 3}
}
```

---

### Summary
- Functions are **general-purpose tools**.
- Methods are **behavior tied to types** and can operate on either a value or a pointer to that type.
- Methods enable object-like behavior, enhancing readability and reusability in Go programs.