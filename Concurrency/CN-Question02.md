# Question 02 - Concurrency

## What is the difference between buffered and unbuffered channels in Go? Provide an example where a buffered channel is more appropriate than an unbuffered channel.

In Go, the difference between buffered and unbuffered channels lies in their capacity and the way they handle communication between goroutines.

### **Unbuffered Channels**
- **Capacity**: An unbuffered channel has no capacity to store values.
- **Behavior**: A send operation (`ch <- value`) on an unbuffered channel blocks until another goroutine performs a receive operation (`value := <-ch`) on the same channel. Similarly, a receive operation blocks until there is a value to receive.
- **Use Case**: Unbuffered channels are suitable when you need strict synchronization between the sender and receiver.

### **Buffered Channels**
- **Capacity**: A buffered channel has a specified capacity to store values.
- **Behavior**: A send operation does not block until the channel's buffer is full. A receive operation does not block until the channel's buffer is empty.
- **Use Case**: Buffered channels are useful when you want to allow sending without immediately requiring a corresponding receive, thus decoupling the sender and receiver.

---

### **Example: Where a Buffered Channel is More Appropriate**

Consider a scenario where you want to process tasks in a producer-consumer model, and the producer generates tasks faster than the consumer can process them. A buffered channel helps to smooth out this difference by allowing the producer to continue producing up to the channel's capacity without waiting for the consumer.

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// Buffered channel with capacity of 3
	tasks := make(chan int, 3)

	// Producer goroutine
	go func() {
		for i := 1; i <= 5; i++ {
			fmt.Printf("Producing task %d\n", i)
			tasks <- i // Send task to the channel (non-blocking until buffer is full)
			time.Sleep(500 * time.Millisecond) // Simulate task production time
		}
		close(tasks) // Close the channel after all tasks are produced
	}()

	// Consumer goroutine
	go func() {
		for task := range tasks {
			fmt.Printf("Consuming task %d\n", task)
			time.Sleep(1 * time.Second) // Simulate task processing time
		}
	}()

	// Give enough time for goroutines to complete
	time.Sleep(6 * time.Second)
}
```

### **Why a Buffered Channel is Appropriate Here**
- **Producer**: Generates tasks every 500ms.
- **Consumer**: Processes tasks every 1 second.
- **Buffered Channel**: Allows the producer to enqueue up to 3 tasks without blocking. If the channel were unbuffered, the producer would have to wait for the consumer to finish processing each task before sending the next one.

### **Output**
```
Producing task 1
Producing task 2
Producing task 3
Consuming task 1
Producing task 4
Consuming task 2
Producing task 5
Consuming task 3
Consuming task 4
Consuming task 5
```

This example demonstrates how a buffered channel helps decouple the producer and consumer, allowing the producer to continue generating tasks even when the consumer is slower.
