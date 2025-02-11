# 1. Composition vs. Inheritance and its Impact on OOP
Question: Can you elaborate on the distinctions between Composition and Inheritance within the

* Composition and Inheritance are two approaches to code reuse, but Go does not support traditional inheritance. Instead, it emphasizes composition for implementing object-oriented programming.

* Traditional Inheritance (Not in Go):
  
```go
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
  
```go
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

* Publisher/Subscriber Architecture
  
```go
package main

import (
	"context"
	"fmt"
	"time"
)

// Publisher function
func publisher(ctx context.Context, ch chan<- string) {
	defer close(ch)
	for i := 0; i < 5; i++ {
		select {
		case <-ctx.Done():
			fmt.Println("Publisher shutting down")
			return
		case ch <- fmt.Sprintf("Message %d", i):
			time.Sleep(1000 * time.Millisecond)
		}
	}
}

// Subscriber function
func subscriber(ctx context.Context, ch <-chan string) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("Subscriber shutting down")
			return
		case msg, ok := <-ch:
			if !ok {
				return
			}
			fmt.Println("Received:", msg)
		}
	}
}

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
	defer cancel()

	ch := make(chan string)
	go publisher(ctx, ch)
	subscriber(ctx, ch)
}

```


# 3. Error Handling in Web Services
Question: Imagine a Go service responsible for handling data storage and retrieval operations for a frontend page. Explain how error
handling should be implemented within this service, particularly focusing on how errors should be propagated back to the frontend.

* Input Validation: Validate request data and return 4XX Bad Request for invalid inputs.
* Service Layer Errors: Use structured errors to propagate meaningful error messages.
* HTTP Response: Map errors to appropriate HTTP status codes and return JSON responses.
* Logging: Log detailed errors on the server side for debugging.


  Gin Framework Sample:
```markdown
```go
package main

import (
	"errors"
	"net/http"

	"github.com/gin-gonic/gin"
)

// Custom error type
type AppError struct {
	Code    int    `json:"code"`
	Message string `json:"message"`
}

func (e *AppError) Error() string {
	return e.Message
}

// Simulated service function
func fetchData(id string) (map[string]string, error) {
	if id == "" {
		return nil, &AppError{Code: http.StatusBadRequest, Message: "Missing ID"}
	}
	if id != "123" {
		return nil, &AppError{Code: http.StatusNotFound, Message: "Data not found"}
	}
	return map[string]string{"id": id, "value": "example"}, nil
}

// Handler function
func getData(c *gin.Context) {
	id := c.Query("id")

	// Call service function
	data, err := fetchData(id)
	if err != nil {
		handleError(c, err)
		return
	}

	// Return success response
	c.JSON(http.StatusOK, gin.H{"data": data})
}

// Error handler
func handleError(c *gin.Context, err error) {
	var appErr *AppError
	if errors.As(err, &appErr) {
		// Return structured error response
		c.JSON(appErr.Code, gin.H{"error": appErr.Message})
	} else {
		// Internal server error
		c.JSON(http.StatusInternalServerError, gin.H{"error": "Internal Server Error"})
		// Log the detailed error for debugging
		c.Error(err)
	}
}

func main() {
	r := gin.Default()

	// Define route
	r.GET("/data", getData)

	// Start server
	r.Run(":8080")
}
```


# 4. Clean Architecture
Question: How would you apply the principles of Clean Architecture when organizing code within a Go application? Provide an example
directory structure and discuss the Go concepts typically employed to effectively implement this architecture.

* Entities: Core business logic, independent of external systems.
* Use Cases: Application-specific business rules, orchestrating interactions between entities and external systems.
* Interface Adapters: Handle input/output, converting data between external systems (e.g., HTTP, DB) and use cases.
* Frameworks/Drivers: External systems like web frameworks, databases, or APIs.

```markdown
project/
├── cmd/                # Application entry points (main package)
│   └── main.go
├── internal/
│   ├── entity/         # Core business entities (domain models)
│   │   └── user.go
│   ├── usecase/        # Application-specific business logic
│   │   └── user_usecase.go
│   ├── adapter/        # Interface adapters (e.g., HTTP handlers, DB repositories)
│   │   ├── http/       # HTTP handlers
│   │   │   └── user_handler.go
│   │   └── repository/ # Database repositories
│   │       └── user_repo.go
│   └── infrastructure/ # Frameworks and drivers (e.g., DB, external APIs)
│       └── db.go
└── pkg/                # Shared libraries/utilities (optional)

```


# 5. Application Performance Optimization
Question: When tasked with optimizing the performance of a Go application, what are the initial areas you focus on? How do you approach
identifying specific areas for improvement? Please share any relevant experiences you have with optimization in Go.

As an SRE, the focus is on ensuring reliability, scalability, and performance. Optimization involves identifying bottlenecks, improving resource utilization, and maintaining system stability.

Key Areas to Focus On:
Profiling and Monitoring:

Use tools like pprof, trace, and expvar to profile CPU, memory, and goroutine usage.
Monitor metrics (latency, throughput, error rates) using Prometheus, Grafana, or similar tools.
Concurrency and Goroutines:

Check for excessive goroutine creation or leaks.
Optimize goroutine usage with worker pools or rate limiting.
Memory Management:

Identify memory leaks or high GC (garbage collection) overhead.
Use pprof to analyze heap allocations and reduce unnecessary memory usage.
I/O and Network:

Optimize database queries, reduce round trips, and use connection pooling.
Use efficient libraries for HTTP or gRPC communication (e.g., fasthttp).
Code Efficiency:

Avoid unnecessary allocations and copies.
Use efficient data structures (e.g., slices vs. maps) and algorithms.
Caching:

Implement caching (e.g., in-memory with sync.Map or external like Redis) to reduce expensive computations or I/O.
Scalability:

Ensure the application scales horizontally by optimizing load balancing and statelessness.
Use rate limiting and circuit breakers to handle traffic spikes.
Approach to Identifying Bottlenecks:
Baseline Metrics:

Establish baseline performance metrics (e.g., latency, CPU, memory usage).
Use distributed tracing (e.g., OpenTelemetry) to identify slow paths.
Reproduce Issues:

Use load testing tools to simulate production-like traffic.
Identify performance degradation under load.
Iterative Optimization:

Focus on the most impactful bottlenecks first
Continuously test and measure improvements.


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
