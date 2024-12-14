# Question 01 - Design Patterns

## Compare and contrast the Factory Method and Abstract Factory patterns in the context of Go. When would you choose one over the other, and how would you handle dependency injection while implementing these patterns in Go?

The **Factory Method** and **Abstract Factory** are two closely related design patterns used to create objects without specifying their concrete classes. Here's a detailed comparison and analysis of when to choose one over the other, along with examples in Go, including considerations for dependency injection.

---

### **1. Factory Method Pattern**
The **Factory Method** defines an interface for creating an object but lets subclasses decide which class to instantiate. This is particularly useful when the object creation process is complex or requires a lot of setup.

#### Example in Go:

Suppose we have a system for creating shapes like `Circle` and `Rectangle`:

```go
package main

import "fmt"

// Shape interface
type Shape interface {
	Draw()
}

// Circle struct
type Circle struct{}

func (c *Circle) Draw() {
	fmt.Println("Drawing a Circle")
}

// Rectangle struct
type Rectangle struct{}

func (r *Rectangle) Draw() {
	fmt.Println("Drawing a Rectangle")
}

// Factory Method Interface
type ShapeFactory interface {
	CreateShape() Shape
}

// CircleFactory struct
type CircleFactory struct{}

func (cf *CircleFactory) CreateShape() Shape {
	return &Circle{}
}

// RectangleFactory struct
type RectangleFactory struct{}

func (rf *RectangleFactory) CreateShape() Shape {
	return &Rectangle{}
}

// Client code
func main() {
	var factory ShapeFactory

	// Create a Circle
	factory = &CircleFactory{}
	shape1 := factory.CreateShape()
	shape1.Draw()

	// Create a Rectangle
	factory = &RectangleFactory{}
	shape2 := factory.CreateShape()
	shape2.Draw()
}
```

#### Key Points:
- The Factory Method pattern provides a way to delegate instantiation logic to subclasses.
- **Use Case**: When you need a single product type but want to encapsulate creation logic.

#### Pros:
- Adheres to the Open-Closed Principle: adding a new `Shape` only requires a new factory implementation.
- Simplifies object creation and reduces dependency on specific classes.

#### Cons:
- Adding a new type requires creating a new factory class, which can result in more boilerplate.

---

### **2. Abstract Factory Pattern**
The **Abstract Factory** pattern provides an interface for creating families of related objects without specifying their concrete classes. It is essentially a factory of factories.

#### Example in Go:

Let's extend the previous example to include a scenario where we need both `Shape` and `Color` objects.

```go
package main

import "fmt"

// Shape interface
type Shape interface {
	Draw()
}

// Circle struct
type Circle struct{}

func (c *Circle) Draw() {
	fmt.Println("Drawing a Circle")
}

// Rectangle struct
type Rectangle struct{}

func (r *Rectangle) Draw() {
	fmt.Println("Drawing a Rectangle")
}

// Color interface
type Color interface {
	Fill()
}

// Red struct
type Red struct{}

func (r *Red) Fill() {
	fmt.Println("Filling with Red color")
}

// Blue struct
type Blue struct{}

func (b *Blue) Fill() {
	fmt.Println("Filling with Blue color")
}

// Abstract Factory Interface
type AbstractFactory interface {
	CreateShape() Shape
	CreateColor() Color
}

// CircleFactory struct
type CircleFactory struct{}

func (cf *CircleFactory) CreateShape() Shape {
	return &Circle{}
}

func (cf *CircleFactory) CreateColor() Color {
	return &Red{} // Circles are red by default
}

// RectangleFactory struct
type RectangleFactory struct{}

func (rf *RectangleFactory) CreateShape() Shape {
	return &Rectangle{}
}

func (rf *RectangleFactory) CreateColor() Color {
	return &Blue{} // Rectangles are blue by default
}

// Client code
func main() {
	var factory AbstractFactory

	// Create a Circle and its Color
	factory = &CircleFactory{}
	shape1 := factory.CreateShape()
	color1 := factory.CreateColor()
	shape1.Draw()
	color1.Fill()

	// Create a Rectangle and its Color
	factory = &RectangleFactory{}
	shape2 := factory.CreateShape()
	color2 := factory.CreateColor()
	shape2.Draw()
	color2.Fill()
}
```

#### Key Points:
- The Abstract Factory pattern allows you to create families of related objects.
- **Use Case**: When you have multiple product families, and you want to ensure they are used together consistently.

#### Pros:
- Ensures consistency among products within a family.
- Makes adding new families easy (e.g., adding `TriangleFactory`).

#### Cons:
- Increases complexity due to additional abstraction layers.

---

### **3. Choosing Between Factory Method and Abstract Factory**
- **Factory Method** is suitable when:
  - You need to create only one type of object at a time.
  - The creation process involves some logic or conditions.

- **Abstract Factory** is suitable when:
  - You need to create multiple related objects that must work together.
  - You want to group related factories under a unified interface.

---

### **4. Handling Dependency Injection in Go**
Dependency Injection (DI) can be easily incorporated into both patterns in Go. For example:

#### Dependency Injection with Factory Method:
Instead of creating factories directly in the client code, you can inject the factory:

```go
type Application struct {
	Factory ShapeFactory
}

func (app *Application) Run() {
	shape := app.Factory.CreateShape()
	shape.Draw()
}

func main() {
	app := &Application{Factory: &CircleFactory{}}
	app.Run()
}
```

#### Dependency Injection with Abstract Factory:
Similarly, inject the Abstract Factory:

```go
type Application struct {
	Factory AbstractFactory
}

func (app *Application) Run() {
	shape := app.Factory.CreateShape()
	color := app.Factory.CreateColor()
	shape.Draw()
	color.Fill()
}

func main() {
	app := &Application{Factory: &CircleFactory{}}
	app.Run()
}
```

#### Key Benefits of DI:
- Makes testing easier by allowing you to inject mock factories.
- Decouples the client code from specific factory implementations.

---

### **Summary Table**

| **Aspect**                  | **Factory Method**                                   | **Abstract Factory**                             |
|-----------------------------|-----------------------------------------------------|-------------------------------------------------|
| **Purpose**                 | Create one product at a time                        | Create families of related products             |
| **Complexity**              | Lower                                               | Higher                                          |
| **Use Case**                | When object creation logic varies per subclass      | When multiple related products need consistency |
| **Dependency Injection**    | Inject the factory                                  | Inject the abstract factory                     |

By carefully analyzing the use case and the complexity of object relationships, you can choose the appropriate pattern and apply DI to make your codebase more flexible and testable.