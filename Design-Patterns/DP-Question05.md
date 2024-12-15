# Question 05 - Design Patterns

## Explain the differences between the Adapter and Bridge design patterns in Go. Provide a real-world example where using each would be appropriate in a Go application, and highlight any specific idiomatic practices in Go for implementing these patterns.

The **Adapter** and **Bridge** design patterns are both structural patterns but serve different purposes and have distinct use cases. Here’s a detailed breakdown of their differences, along with real-world examples and idiomatic Go practices:

---

### **1. Adapter Design Pattern**
**Purpose:**  
The Adapter pattern is used to make two incompatible interfaces compatible with each other. It works as a bridge between a client and a service by translating the interface of the service into a format the client can understand.

**Key Characteristics:**
- Focuses on **interface conversion** to ensure compatibility.
- Typically used to integrate legacy or third-party code without modifying it.

#### **Real-World Example in Go:**
Imagine you are building a Go application that uses a third-party library for logging, but the library’s logging interface is different from the one your application expects.

```go
// Existing logger library (third-party or legacy)
type ExternalLogger struct {}

func (e *ExternalLogger) LogMessage(message string) {
	fmt.Println("External Logger:", message)
}

// Your application's expected logging interface
type Logger interface {
	Log(message string)
}

// Adapter to make ExternalLogger compatible with Logger
type LoggerAdapter struct {
	externalLogger *ExternalLogger
}

func (a *LoggerAdapter) Log(message string) {
	a.externalLogger.LogMessage(message) // Adapt the method call
}

// Usage
func main() {
	externalLogger := &ExternalLogger{}
	logger := &LoggerAdapter{externalLogger: externalLogger}
	logger.Log("Hello, Adapter Pattern!") // Works seamlessly
}
```

#### **Idiomatic Go Practices for Adapter:**
- Use composition rather than inheritance (e.g., embedding the external object in the adapter).
- Leverage Go’s interface-based design for flexibility.
- Keep the adapter small and focused on translating between interfaces.

---

### **2. Bridge Design Pattern**
**Purpose:**  
The Bridge pattern is used to separate an abstraction from its implementation so that both can vary independently. It decouples the interface (abstraction) from the actual implementation, allowing flexibility in extending both dimensions.

**Key Characteristics:**
- Focuses on **decoupling abstraction and implementation**.
- Useful when you have multiple orthogonal dimensions of variation.
- Promotes composition over inheritance.

#### **Real-World Example in Go:**
Suppose you're building a drawing application that can render shapes in different formats (e.g., vector and raster).

```go
// Implementor: Rendering implementation interface
type Renderer interface {
	RenderCircle(radius float64)
}

// Concrete Implementors: Different rendering implementations
type VectorRenderer struct {}

func (v *VectorRenderer) RenderCircle(radius float64) {
	fmt.Printf("Drawing a circle with radius %.2f in vector format\n", radius)
}

type RasterRenderer struct {}

func (r *RasterRenderer) RenderCircle(radius float64) {
	fmt.Printf("Drawing a circle with radius %.2f in raster format\n", radius)
}

// Abstraction: Shape interface
type Shape interface {
	Draw()
	SetRenderer(renderer Renderer)
}

// Refined Abstraction: Circle
type Circle struct {
	radius   float64
	renderer Renderer
}

func (c *Circle) Draw() {
	c.renderer.RenderCircle(c.radius) // Delegates to the implementor
}

func (c *Circle) SetRenderer(renderer Renderer) {
	c.renderer = renderer
}

// Usage
func main() {
	vectorRenderer := &VectorRenderer{}
	rasterRenderer := &RasterRenderer{}

	// Use the bridge to vary both the abstraction and the implementation
	circle := &Circle{radius: 5}
	circle.SetRenderer(vectorRenderer)
	circle.Draw()

	circle.SetRenderer(rasterRenderer)
	circle.Draw()
}
```

#### **Idiomatic Go Practices for Bridge:**
- Define small interfaces (e.g., `Renderer`) to ensure flexibility and ease of testing.
- Use dependency injection to provide different implementations at runtime.
- Favor composition and delegation over deep inheritance hierarchies.

---

### **Key Differences Between Adapter and Bridge:**

| Aspect                | Adapter Pattern                                                                 | Bridge Pattern                                                           |
|-----------------------|---------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| **Purpose**           | Converts one interface into another to ensure compatibility.                   | Decouples abstraction from implementation to allow both to evolve independently. |
| **Focus**             | Solves an incompatibility issue between two interfaces.                        | Separates concerns, enabling independent extension of abstraction and implementation. |
| **Use Case**          | Integrating third-party or legacy components.                                  | Managing multiple orthogonal variations (e.g., shape and rendering type). |
| **Relationship**      | Typically adapts an existing object (focuses on fixing one side).              | Establishes a two-way relationship between abstraction and implementation. |
| **Go Idioms**         | Small adapters using composition.                                              | Dependency injection and small, composable interfaces.                   |

---

### **Choosing the Right Pattern:**
- Use **Adapter** when integrating incompatible interfaces without modifying existing code.
- Use **Bridge** when you need to support multiple dimensions of variation that should evolve independently.

By understanding these differences and leveraging Go’s idiomatic practices (such as small interfaces, composition, and dependency injection), you can implement these patterns effectively in Go applications.