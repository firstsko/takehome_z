# 1. Composition vs. Inheritance and its Impact on OOP
Question: Can you elaborate on the distinctions between Composition and Inheritance within the

* Composition and Inheritance are two approaches to code reuse, but Go does not support traditional inheritance. Instead, it emphasizes composition for implementing object-oriented programming.

** Traditional Inheritance (Not in Go):
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

** Go Composition:
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
