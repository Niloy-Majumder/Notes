
## Introduction to Go

- Go (Golang): Open-source language by Google, designed for simplicity, readability, and efficiency.
- Concurrency support and garbage collection are key features.
- Statically typed with a syntax similar to C.

## Basics

### Hello World

```Go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

- Always start with `package main` for executable programs.
- Import necessary packages, in this case, "fmt" for formatting and printing.

### Variables

```Go
package main

import "fmt"

func main() {
    // Declare and initialize variables
    var name string = "John"
    age := 25
	const tickets uint = 50
	
    // Print variables
    fmt.Printf("Name: %s\nAge: %d\nTickets: %v\n", name, age,tickets)
}
```

- Explicit and implicit variable declaration.
- Use `%s` for strings and `%d` for integers in `Printf` for formatting and `%v` for all variable.

### Control Flow

```Go
package main

import "fmt"

func main() {
    // If-else statement
    score := 85
    if score >= 90 {
        fmt.Println("Excellent!")
    } else if score >= 70 {
        fmt.Println("Good job!")
    } else {
        fmt.Println("You can do better.")
    }

    // Switch statement
    day := "Monday"
    switch day {
    case "Monday", "Tuesday":
        fmt.Println("It's a weekday.")
    case "Saturday", "Sunday":
        fmt.Println("It's the weekend.")
    default:
        fmt.Println("Not a valid day.")
    }
}
```

- Handle all possible cases in `if-else` and `switch` statements.
- Account for unexpected inputs.

## Arrays

### Declaration and Initialization

```Go
package main

import "fmt"

func main() {
    var numbers [5]int
    numbers = [5]int{1, 2, 3, 4, 5}

    fmt.Println(numbers)

    // Iterating over an array using a loop.
    for i := 0; i < len(numbers); i++ {
        fmt.Printf("%d ", numbers[i])
    }
}
```

- Arrays are fixed in size at compile-time.
- Zero-based indexing is used for accessing elements.
- Utilise a loop and `len` function for array iteration.

## Slices

### Declaration and Initialization, Modifying, and Slicing

```Go
package main

import "fmt"

func main() {
    numbers := []int{1, 2, 3, 4, 5}

    // Modifying slices by appending an element.
    numbers = append(numbers, 6)

    // Slicing a slice to create a new view.
    slicedNumbers := numbers[1:3]

    fmt.Println(numbers)
    fmt.Println(slicedNumbers)
}
```

 - Slices are dynamic and can change in size during runtime.
- A slice is a reference to an underlying array.

## Basic Loop Structures

### 1. For Loop

```Go
package main

import "fmt"

func main() {
    // Basic for loop
    for i := 1; i <= 5; i++ {
        fmt.Printf("%d ", i)
    }
}
```

- Standard `for` loop with initialisation, condition, and post statement.

### 2. While-like Loop

```Go
package main

import "fmt"

func main() {
    // While-like loop
    count := 0
    for count < 5 {
        fmt.Printf("%d ", count)
        count++
    }
}
```

- Similar to a `while` loop in other languages.

### 3. Infinite Loop

```Go
package main

import "fmt"

func main() {
    // Infinite loop
    for {
        fmt.Println("This loop is infinite.")
        // Use break or return to exit the loop
        // break
    }
}
```

- A loop without a condition runs indefinitely until explicitly broken.

### 4. For-each Loop (Range)

```Go
package main

import "fmt"

func main() {
    // For-each loop using range
    numbers := []int{1, 2, 3, 4, 5}
    for index, value := range numbers {
        fmt.Printf("Index: %d, Value: %d\n", index, value)
    }
}
```

- Iterates over elements of an array, slice, or map.

## Functions

### Basic Function

```Go
package main

import "fmt"

// Function definition
func add(a, b int) int {
    return a + b
}

func main() {
    // Function call
    result := add(3, 7)
    fmt.Println("Sum:", result)
}
```

- Clearly define input parameters and expected output.
- Consider potential edge cases for the function's behaviour.

### Anonymous Functions

```Go
package main

import "fmt"

func main() {
    // Anonymous function
    multiply := func(x, y int) int {
        return x * y
    }

    result := multiply(4, 5)
    fmt.Println("Product:", result)
}
```

- Explore the use of anonymous functions for flexibility.
- Ensure proper scoping and access to variables.

## Introduction to Pointers

- Pointers store the memory address of a variable.
- Pointers allow indirect access to the value stored in memory.

### Declaration and Initialization

```Go
package main

import "fmt"

func main() {
	// Declaration and Initialization of a pointer
    var x int = 42
    var ptr *int = &x

    fmt.Println(x)    // 42
    fmt.Println(ptr)  // Memory address of x
    fmt.Println(*ptr) // Value at the memory address stored in ptr(dereferencing)
}
```

- `&` operator is used to get the memory address of a variable.
- `*` operator is used for de-referencing, to access the value at the memory address.

### Null Pointers

```Go
package main

import "fmt"

func main() {
    // Null pointers in Go are represented by nil
    var ptr *int

    fmt.Println(ptr)  // nil
    // fmt.Println(*ptr)  // This would result in a runtime error
}
```

- Pointers in Go are initialized with `nil` by default if not explicitly assigned.

### Pointers as Function Parameters

```Go
package main

import "fmt"

func modifyValue(val *int) {
    *val = 20
}

func main() {
    // Passing a pointer to a function to modify the original value
    x := 10
    modifyValue(&x)

    fmt.Println(x)  // 20
}
```

- Passing a pointer to a function allows the function to modify the original value.

## Maps

- Maps are key-value pairs, providing an efficient way to represent relationships between data.
- Keys and values can be of any type, as long as the key is comparable.

### Declaration and Initialization

```Go
package main

import "fmt"

func main() {
    // Declaration and Initialization of a map
    studentGrades := map[string]int{
        "Alice":  90,
        "Bob":    85,
        "Charlie": 92,
    }
    languages := make(map[string]string)

    fmt.Println(studentGrades)  // map[Alice:90 Bob:85 Charlie:92]
}
```

- `make` function is used to create an empty map.

### Accessing, Modifying, Checking Existence, Deleting

```Go
package main

import "fmt"

func main() {
    // Declaration and Initialization of a map
    studentGrades := map[string]int{
        "Alice":  90,
        "Bob":    85,
        "Charlie": 92,
    }

    fmt.Println(studentGrades)  // map[Alice:90 Bob:85 Charlie:92]

    // Accessing and modifying map elements
    fmt.Println(studentGrades["Alice"])  // 90

    // Modifying value
    studentGrades["Bob"] = 88
    fmt.Println(studentGrades)  // map[Alice:90 Bob:88 Charlie:92]

    // Checking if a key exists in a map
    score, exists := studentGrades["Bob"]
    if exists {
        fmt.Println("Bob's score:", score)
    } else {
        fmt.Println("Bob's score not found.")
    }

    // Deleting elements from a map
    delete(studentGrades, "Bob")
    fmt.Println(studentGrades)  // map[Alice:90 Charlie:92]
}

```

### Iterating Over a Map

```Go
package main

import "fmt"

func main() {
    // Iterating over a map
    studentGrades := map[string]int{
        "Alice":  90,
        "Bob":    85,
        "Charlie": 92,
    }

    for name, score := range studentGrades {
        fmt.Printf("%s's score: %d\n", name, score)
    }
}
```

- Use `range` to iterate over the key-value pairs in a map.

## Structs

- Structs allow you to group together variables of different types under a single name.
- A struct is a composite data type that groups variables.

### Declaration and Initialization

```Go
package main

import "fmt"

func main() {
    // Declaration and Initialization of a struct
    type Person struct {
        Name string
        Age  int
    }

    // Initializing a struct
    person1 := Person{"Alice", 25}
    person2 := Person{
        Name: "Bob",
        Age:  30,
    }

    fmt.Println(person1)  // {Alice 25}
    fmt.Println(person2)  // {Bob 30}

    // Accessing and modifying struct fields
    person := Person{"Alice", 25}
    fmt.Println(person.Name)  // Alice

    // Modifying struct field
    person.Age = 26
    fmt.Println(person)  // {Alice 26}
}
```

- `type` keyword is used to define a new type (struct).
- Variables can be initialized using curly braces `{}`.

### Nested Structs

```Go
package main

import "fmt"

func main() {
    // Nested structs
    type Contact struct {
        Email   string
        Phone   string
    }

    type Person struct {
        Name    string
        Age     int
        Contact Contact
    }

    person := Person{
        Name: "David",
        Age:  28,
        Contact: Contact{
            Email: "david@example.com",
            Phone: "123-456-7890",
        },
    }

    fmt.Println(person)  // {David 28 {david@example.com 123-456-7890}}
}
```


## Methods

### Method Declaration and Syntax

```Go
package main

import "fmt"

// Declaration of a type
type Rectangle struct {
    Width  float64
    Height float64
}

// Method for the Rectangle type
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func main() {
    // Creating an instance of the Rectangle type
    rect := Rectangle{Width: 5, Height: 3}

    // Calling the method
    area := rect.Area()
    fmt.Println("Area:", area)  // Area: 15
}
```

- Method syntax: `func (receiver Type) methodName() returnType { ... }`
- `receiver` is the instance the method is called on (similar to `this` or `self` in other languages).

### Value Receiver vs Pointer Receiver

```Go
package main

import "fmt"

type Rectangle struct {
    Width  float64
    Height float64
}

// Value Receiver Method
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Pointer Receiver Method
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

func main() {
    rect := Rectangle{Width: 5, Height: 3}

    // Calling value receiver method
    area := rect.Area()
    fmt.Println("Area:", area)  // Area: 15

    // Calling pointer receiver method
    rect.Scale(2)
    fmt.Println("Scaled Rectangle:", rect)  // Scaled Rectangle: {10 6}
}
```

- Value receivers receive a copy of the struct.
- Pointer receivers receive a reference to the struct.
- Go automatically de-references the pointer when you access fields or methods on structs.

## Interfaces

 An interface is a type that defines a set of method signatures (a contract), but does not implement them. If a type implements all the methods declared in an interface, it is said to satisfy that interface — implicitly.

```go
package main

import "fmt"

// Interface
type Speaker interface {
    Speak() string
}

// Structs
type Dog struct{}
type Human struct{}

// Methods
func (d Dog) Speak() string {
    return "Woof!"
}

func (h Human) Speak() string {
    return "Hello!"
}

// Function using interface
func SaySomething(s Speaker) {
    fmt.Println(s.Speak())
}

func main() {
    SaySomething(Dog{})    // Woof!
    SaySomething(Human{})  // Hello!
}

```

**Empty interface** `interface{}` Can hold any value (like `Object` in other languages). Useful for generic-like behavior.

