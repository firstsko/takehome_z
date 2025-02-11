# 1. Composition vs. Inheritance and its Impact on OOP
Question: Can you elaborate on the distinctions between Composition and Inheritance within the

* Composition and Inheritance are two approaches to code reuse, but Go does not support traditional inheritance. Instead, it emphasizes composition for implementing object-oriented programming.

* Traditional Inheritance (Not in Go):
```
class Animal {
    void Speak() {
        System.out.println("Animal speaks");
    }
}

class Dog extends Animal {
    void Speak() {
        System.out.println("Dog barks");
    }
}

```

* Go Composition:
```
package main

import "fmt"

// Base struct
type Animal struct{}

func (a Animal) Speak() {
    fmt.Println("Animal speaks")
}

// Composed struct
type Dog struct {
    Animal // Embedding Animal struct
}

func (d Dog) Speak() {
    fmt.Println("Dog barks")
}

func main() {
    dog := Dog{}
    dog.Speak()       // Output: Dog barks
    dog.Animal.Speak() // Output: Animal speaks
}

```

# 2. Asynchronous Workflows
Question: Describe the process of creating, managing, and gracefully shutting down asynchronous workflows within a Go application.
Provide an example demonstrating a publisher/subscriber architecture implemented using these methods without relying on external
infrastructure. Additionally, explain the significance of properly shutting down these workflows.

* asynchronous workflows are typically managed using goroutines, channels, and context for coordination and graceful shutdown. Here's how to create, manage, and shut them down:
* Creating Workflows（Use goroutines to perform tasks concurrently）  ---》 Managing Workflows （publisher/subscriber pattern） ---》Graceful Shutdown（Use context.WithCancel or context.WithTimeout to signal goroutines to stop and ensure resources are cleaned up）


: Use channels for communication between goroutines (e.g., publisher/subscriber pattern).
Graceful Shutdown: Use context.WithCancel or context.WithTimeout to signal goroutines to stop and ensure resources are cleaned up.


# 3. Error Handling in Web Services
Question: Imagine a Go service responsible for handling data storage and retrieval operations for a frontend page. Explain how error
handling should be implemented within this service, particularly focusing on how errors should be propagated back to the frontend.


# 4. Clean Architecture
Question: How would you apply the principles of Clean Architecture when organizing code within a Go application? Provide an example
directory structure and discuss the Go concepts typically employed to effectively implement this architecture.


# 5. Application Performance Optimization
Question: When tasked with optimizing the performance of a Go application, what are the initial areas you focus on? How do you approach
identifying specific areas for improvement? Please share any relevant experiences you have with optimization in Go.


# 6. Generic Processing of Structures
Question: In Go, what is your preferred development pattern for abstracting the instantiation and processing of multiple data structures in a
generic manner? Explain your reasoning and provide an example demonstrating the use of this pattern to instantiate different structure
types while maintaining a consistent processing approach.


# 7. Microservice Architecture: Reducing Inter-Service Communication
Question: Consider a high-traffic, low-latency product that has transitioned from a legacy monolithic architecture to a microservice-based
architecture. Each service adheres to the Single Responsibility Principle (SRP) for data management, requiring inter-service communication
to complete tasks. What additional architectural patterns or best practices could be implemented to minimize inter-service communication
and enhance overall system throughput and latency?


# 8. Improve Concurrency
Question: What strategies can be employed to optimize application-level concurrency when working with database constraints, particularly
in scenarios with:
Shared database infrastructure
Fixed connection limits
Immutable cluster configurations
