### Complete Go Tutorial: Basic to Advanced

This tutorial will take you from writing your first Go program to building backend APIs, concurrent applications, CLI tools, and terminal user interfaces (TUIs) with Go.

Since you're interested in Java, Spring Boot, and terminal interfaces like Bubble Tea, I'll explain Go concepts alongside Java comparisons and practical projects.

### Go learning roadmap

1\. Fundamentals

Installation, syntax, variables, data types, operators, input/output, conditions, loops, functions.

2\. Core Go

Arrays, slices, maps, pointers, structs, methods, interfaces, errors, packages, modules.

3\. Intermediate Go

Generics, closures, defer, panic/recover, JSON, files, testing, reflection.

4\. Advanced Go

Goroutines, channels, select, mutexes, context, worker pools, memory and performance.

5\. Real-world development

REST APIs, PostgreSQL, authentication, Docker, CLI applications, Bubble Tea TUI and deployment.

### 1\. What is Go?

Go (also called Golang) is a statically typed, compiled programming language developed at Google.

It is commonly used for:

* Backend APIs and microservices.

* Cloud infrastructure and DevOps tools.

* Networking and concurrent applications.

* Command-line tools.

* Terminal user interfaces using libraries such as Bubble Tea.

### Go vs Java

| Feature               | Go                              | Java                         |
| --------------------- | ------------------------------- | ---------------------------- |
| Execution             | Compiles to native machine code | Usually runs on the JVM      |
| Entry point           | func main()                     | public static void main(...) |
| Object model          | Structs and interfaces          | Classes and interfaces       |
| Inheritance           | No class inheritance            | Supports class inheritance   |
| Concurrency           | Goroutines and channels         | Threads and concurrency APIs |
| Error handling        | Explicit error values           | Exceptions                   |
| Dependency management | Go modules                      | Maven or Gradle              |
| Memory management     | Garbage collection              | Garbage collection           |

### 2\. Install Go on Fedora Linux

Install the Go compiler using Fedora's package manager:

```bash
sudo dnf install golang
```

Verify the installation:

```bash
go version
```

Check the environment:

```bash
go env
```

Create a project:

```bash
mkdir go-tutorial
cd go-tutorial
go mod init example.com/go-tutorial
```

Create a file named `main.go`.

### 3\. Your first Go program

Add this code to `main.go`:

```go

package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

Run it:

```bash
go run main.go
```

Build an executable:

```bash
go build -o app
./app
```

### Understand the code

* `package main`: declares an executable program.

* `import "fmt"`: imports the formatting and input/output package.

* `func main()`: the program's entry point.

* `fmt.Println()`: prints text followed by a newline.

Unlike Java, you don't need to declare a class around your main function.

### 4\. Variables and constants

Go has explicit and inferred types.

```go

package main

import "fmt"

func main() {
    var name string = "Pavan"
    var age int = 22

    city := "Pune"
    score := 85.5

    const language = "Go"

    fmt.Println(name)
    fmt.Println(age)
    fmt.Println(city)
    fmt.Println(score)
    fmt.Println(language)
}
```

### Important syntax

```go
var age int = 22
var count int
count = 10

name := "Go" // Short declaration inside a function

const pi = 3.14159
```

`:=` declares and initializes a variable, with Go inferring its type. You can use it inside functions, not directly at package level.

Variables declared but not explicitly initialized receive zero values:

| Type                                              | Zero value |
| ------------------------------------------------- | ---------- |
| int                                               | 0          |
| float64                                           | 0.0        |
| bool                                              | false      |
| string                                            | ""         |
| Pointer, slice, map, function, channel, interface | nil        |

Go rejects unused local variables and unused imported packages. This helps catch mistakes early.

### 5\. Data types

```go

package main

import "fmt"

func main() {
    var age int = 22
    var price float64 = 99.99
    var active bool = true
    var letter rune = 'G'
    var message string = "Hello"

    fmt.Printf("%T\n", age)
    fmt.Printf("%T\n", price)
    fmt.Printf("%T\n", active)
    fmt.Printf("%T\n", letter)
    fmt.Printf("%T\n", message)
}
```

Common types:

* Integers: `int`, `int8`, `int16`, `int32`, `int64`

* Unsigned integers: `uint`, `uint8`, `uint16`, `uint32`, `uint64`

* Floating point: `float32`, `float64`

* Boolean: `bool`

* Text: `string`

* Unicode code point: `rune`

* Single byte: `byte` (alias for `uint8`)

A `rune` represents a Unicode code point, not necessarily one user-perceived character.

### Type conversion

Go generally requires explicit conversion between different numeric types.

```go
var a int = 10
var b float64 = float64(a)

fmt.Println(b) // 10
```

Unlike Java, Go does not implicitly convert arbitrary numeric types for you.

### 6\. Input and output

Use `fmt` for basic console input.

```go

package main

import "fmt"

func main() {
    var name string
    var age int

    fmt.Print("Enter name: ")
    fmt.Scan(&name)

    fmt.Print("Enter age: ")
    fmt.Scan(&age)

    fmt.Printf("Hello %s, age %d\n", name, age)
}
```

Notice `&name` and `&age`: these pass pointers to variables so `Scan` can update them.

Useful formatting verbs:

| Verb | Meaning                |
| ---- | ---------------------- |
| %s   | String                 |
| %d   | Integer                |
| %f   | Floating-point number  |
| %.2f | Two decimal places     |
| %t   | Boolean                |
| %T   | Variable's type        |
| %v   | Default representation |

For input containing spaces, such as a full name, use `bufio.Reader` or `bufio.Scanner` instead of relying on `fmt.Scan`.

### 7\. Operators

Go supports arithmetic, comparison, logical, and assignment operators.

```go
a := 10
b := 3

fmt.Println(a + b)  // 13
fmt.Println(a - b)  // 7
fmt.Println(a * b)  // 30
fmt.Println(a / b)  // 3
fmt.Println(a % b)  // 1

fmt.Println(a > b)        // true
fmt.Println(a == b)       // false
fmt.Println(a > 5 && b < 5) // true
fmt.Println(a > 5 || b > 5) // true
```

Integer division truncates the fractional part. For example, `10 / 3` produces `3` when both operands are integers.

Go has no `++` or `--` expressions, but it does have increment and decrement statements:

```go
a++
a--
```

You cannot write `b = a++`.

### 8\. Conditions and switch

### If / else

```go

package main

import "fmt"

func main() {
    marks := 75

    if marks >= 90 {
        fmt.Println("Grade A")
    } else if marks >= 60 {
        fmt.Println("Grade B")
    } else {
        fmt.Println("Needs improvement")
    }
}
```

Go doesn't use parentheses around the condition.

You can also initialize a variable in an `if` statement:

```go
if score := 80; score >= 60 {
    fmt.Println("Passed", score)
}
```

`score` is scoped to the `if` statement and its `else` branches.

### Switch

```go

package main

import "fmt"

func main() {
    day := 2

    switch day {
    case 1:
        fmt.Println("Monday")
    case 2:
        fmt.Println("Tuesday")
    case 3:
        fmt.Println("Wednesday")
    default:
        fmt.Println("Other day")
    }
}
```

Go automatically stops after a matching case unless you explicitly use `fallthrough`.

### 9\. Loops

Go has one loop keyword: `for`.

### Traditional loop

```go
for i := 1; i <= 5; i++ {
    fmt.Println(i)
}
```

### While-style loop

```go
count := 1

for count <= 5 {
    fmt.Println(count)
    count++
}
```

### Infinite loop

```go
for {
    fmt.Println("Running")
    break
}
```

### Range loop

```go
numbers := []int{10, 20, 30}

for index, value := range numbers {
    fmt.Println(index, value)
}
```

Use `_` to ignore a value you don't need:

```go
for _, value := range numbers {
    fmt.Println(value)
}
```

Practical uses: loops are useful for processing database results, reading files, iterating over API responses, and rendering menu items in a TUI.

### 10\. Functions

Functions are first-class values in Go. They can accept parameters, return values, and be passed to other functions.

```go

package main

import "fmt"

func add(a int, b int) int {
    return a + b
}

func divide(a, b float64) (float64, bool) {
    if b == 0 {
        return 0, false
    }

    return a / b, true
}

func main() {
    fmt.Println(add(10, 20))

    result, ok := divide(10, 2)
    if ok {
        fmt.Println(result)
    }
}
```

Notice that `a, b float64` is shorthand for declaring both parameters with the same type.

### Named return values

```go
func rectangle(width, height float64) (area float64) {
    area = width * height
    return
}
```

Named returns can help in some functions, but explicit return expressions are often easier to read.

### Variadic functions

```go
func sum(numbers ...int) int {
    total := 0

    for _, n := range numbers {
        total += n
    }

    return total
}
```

Call it with any number of integer arguments:

```go
fmt.Println(sum(1, 2, 3, 4))
```

### Anonymous functions and closures

```go
func main() {
    multiplier := 3

    doubleLike := func(n int) int {
        return n * multiplier
    }

    fmt.Println(doubleLike(10)) // 30
}
```

A closure can access variables from its surrounding scope.

### 11\. Arrays and slices

This is one of the most important Go concepts.

### Arrays

An array has a fixed length, and the length is part of its type.

```go
var numbers [3]int = [3]int{10, 20, 30}
fmt.Println(numbers)
```

An array of three integers and an array of four integers are different types.

### Slices

A slice is a flexible view over an underlying array.

```go

package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30}

    numbers = append(numbers, 40)

    fmt.Println(numbers)
    fmt.Println(numbers[0])
    fmt.Println(numbers[1:3])
    fmt.Println(len(numbers))
    fmt.Println(cap(numbers))
}
```

Output:

```text
[10 20 30 40]
10
[20 30]
4
4
```

* `len()` returns the current length.

* `cap()` returns the capacity of the slice.

* `append()` adds elements and returns a slice that you should assign back.

### Make a slice

```go
numbers := make([]int, 3, 10)
```

This creates a slice with length 3 and capacity 10. Its initial values are all zero.

### How slices work internally

A slice contains a reference to an underlying array, a length, and a capacity. Multiple slices can share the same array.

```go
a := []int{1, 2, 3}
b := a[:2]

b[0] = 99

fmt.Println(a) // [99 2 3]
```

If `append` exceeds the available capacity, Go allocates a larger underlying array as needed. Code should not assume that two slices always share storage.

### 12\. Maps

A map stores key-value pairs, similar to Java's `HashMap`.

```go

package main

import "fmt"

func main() {
    scores := map[string]int{
        "Amit": 90,
        "Ravi": 80,
    }

    scores["Neha"] = 95

    fmt.Println(scores["Amit"])

    value, exists := scores["Pavan"]
    fmt.Println(value, exists)

    delete(scores, "Ravi")

    for name, score := range scores {
        fmt.Println(name, score)
    }
}
```

The comma-ok pattern:

```go
value, exists := scores["Pavan"]
```

lets you distinguish a missing key from a key holding the zero value.

Important facts:

* Map iteration order is unspecified.

* Maps must be initialized before inserting entries; a nil map can be read but not written.

* Maps are not safe for concurrent writes without synchronization.

### 13\. Pointers

A pointer stores the address of a value.

```go

package main

import "fmt"

func main() {
    age := 22

    ptr := &age

    fmt.Println(age)  // 22
    fmt.Println(ptr)  // Address
    fmt.Println(*ptr) // 22

    *ptr = 23

    fmt.Println(age) // 23
}
```

* `&age` obtains the address.

* `*ptr` dereferences a pointer.

* `*ptr = 23` updates the original variable.

### Pass by value

Go passes arguments by value, including pointers. Passing a pointer copies the pointer value, allowing the function to access and modify the pointed-to data.

```go
func change(n *int) {
    *n = 100
}
```

Java has references too, but Go exposes pointers directly. Go does not support pointer arithmetic in ordinary safe Go code.

### 14\. Structs and methods

Go uses structs to group related fields.

```go

package main

import "fmt"

type User struct {
    Name string
    Age  int
}

func (u User) Introduce() string {
    return fmt.Sprintf("I am %s, age %d", u.Name, u.Age)
}

func (u *User) HaveBirthday() {
    u.Age++
}

func main() {
    user := User{Name: "Amit", Age: 22}

    fmt.Println(user.Introduce())

    user.HaveBirthday()

    fmt.Println(user.Age)
}
```

`Introduce()` has a value receiver, while `HaveBirthday()` has a pointer receiver.

Unlike Java, Go doesn't require a class to define methods. A method is a function with a receiver.

### Composition instead of inheritance

```go
type Address struct {
    City string
}

type Employee struct {
    Name string
    Address
}
```

`Employee` embeds `Address`, promoting its fields and methods where applicable. This supports composition without a traditional class inheritance hierarchy.

### 15\. Interfaces

Interfaces describe behavior rather than a concrete implementation. This is one of Go's most powerful features.

```go
type Speaker interface {
    Speak() string
}

type Dog struct{}

func (d Dog) Speak() string {
    return "Woof"
}

type Person struct{}

func (p Person) Speak() string {
    return "Hello"
}

func introduce(s Speaker) {
    fmt.Println(s.Speak())
}
```

Usage:

```go
introduce(Dog{})
introduce(Person{})
```

Both types satisfy `Speaker` automatically because they implement its methods. Go does not require an explicit `implements` declaration.

### Go interface vs Java interface

| Java                                             | Go                              |
| ------------------------------------------------ | ------------------------------- |
| Usually declares implements                      | Implementation is implicit      |
| Can have default methods                         | Interfaces describe method sets |
| Class inheritance available                      | Composition is preferred        |
| Interfaces often defined around domain contracts | Small interfaces are common     |

An empty interface, `any`, can hold values of any type:

```go
var value any = "hello"
fmt.Println(value)
```

Use type assertions or type switches to inspect its concrete type when necessary.

### 16\. Error handling

Go typically returns errors as values instead of throwing exceptions for ordinary failures.

```go

package main

import (
    "errors"
    "fmt"
)

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }

    return a / b, nil
}

func main() {
    result, err := divide(10, 0)

    if err != nil {
        fmt.Println("Error:", err)
        return
    }

    fmt.Println(result)
}
```

### Wrapping errors

Use `fmt.Errorf` with `%w` to add context while preserving the underlying error:

```go
return fmt.Errorf("loading user: %w", err)
```

You can inspect wrapped errors with `errors.Is` and `errors.As`.

Best practice: handle errors where you can make a meaningful decision. Don't ignore an error simply by assigning it to `_`.

### 17\. Defer, panic and recover

### Defer

A deferred function runs when its surrounding function returns.

```go
func main() {
    defer fmt.Println("Third")
    defer fmt.Println("Second")

    fmt.Println("First")
}
```

Output:

```text
First
Second
Third
```

Deferred calls execute in last-in, first-out order. They are frequently used to close files, release locks, and clean up resources.

### Panic and recover

`panic` stops normal execution in the current goroutine and begins unwinding its deferred calls. `recover` can intercept a panic from a deferred function in the same goroutine.

```go
func safeOperation() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered:", r)
        }
    }()

    panic("something went wrong")
}
```

Do not use `panic` as a replacement for normal error handling. It is generally reserved for unrecoverable programming errors or exceptional situations.

### 18\. Packages and modules

A package organizes related Go source files. A module is a versioned collection of packages managed through `go.mod`.

Example project:

```text
myapp/
├── go.mod
├── main.go
├── models/
│   └── user.go
├── service/
│   └── user_service.go
└── repository/
    └── user_repository.go
```

Initialize it:

```bash
go mod init example.com/myapp
```

Import a package:

```go
import "example.com/myapp/models"
```

### Exported vs unexported names

```go
type User struct {
    Name string // Exported
    age  int    // Unexported
}
```

Names beginning with an uppercase letter are exported outside the package; lowercase names are unexported.

Useful commands:

```bash
go mod tidy
go get example.com/some/library
go list ./...
go test ./...
```

`go mod tidy` reconciles module requirements with the imports in your source code.

### 19\. File handling

Use `os` and `io` packages to work with files.

### Write a file

```go
package main

import (
    "os"
)

func main() {
    err := os.WriteFile(
        "hello.txt",
        []byte("Hello from Go"),
        0644,
    )

    if err != nil {
        panic(err)
    }
}
```

### Read a file

```go
data, err := os.ReadFile("hello.txt")
if err != nil {
    panic(err)
}

fmt.Println(string(data))
```

In production code, return or handle the error rather than panicking.

For large files, streams, or line-by-line processing, use `os.Open` with `bufio.Scanner` or an appropriate reader rather than loading the whole file into memory.

### 20\. JSON handling

Go's `encoding/json` package is commonly used for REST APIs and configuration.

```go
package main

import (
    "encoding/json"
    "fmt"
)

type User struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    user := User{Name: "Amit", Age: 22}

    data, err := json.Marshal(user)
    if err != nil {
        panic(err)
    }

    fmt.Println(string(data))

    var decoded User
    err = json.Unmarshal(data, &decoded)
    if err != nil {
        panic(err)
    }

    fmt.Println(decoded.Name)
}
```

The JSON tags control how struct fields appear in JSON.

Output:

```json
{"name":"Amit","age":22}
```

This is similar to using Jackson to serialize and deserialize Java objects in Spring Boot.

### 21\. Generics

Generics let you write reusable functions and types that work with multiple types.

```go
package main

import "fmt"

func First[T any](values []T) T {
    return values[0]
}

func main() {
    fmt.Println(First([]int{10, 20, 30}))
    fmt.Println(First([]string{"Go", "Java"}))
}
```

`T` is a type parameter. `any` means any type is allowed.

For numeric operations, define a constraint that lists the permitted types. You cannot use arbitrary operators on every type just because the type parameter is declared with `any`.

Important: the example assumes the slice is non-empty. A production version should handle an empty slice, perhaps by returning `(T, bool)`.

### 22\. Concurrency: Goroutines

Go's concurrency model is a major reason to learn the language.

A goroutine is a lightweight unit of concurrent execution managed by the Go runtime.

```go

package main

import (
    "fmt"
    "time"
)

func task(name string) {
    for i := 1; i <= 3; i++ {
        fmt.Println(name, i)
        time.Sleep(100 * time.Millisecond)
    }
}

func main() {
    go task("Worker")

    task("Main")

    time.Sleep(400 * time.Millisecond)
}
```

The `go` keyword starts a goroutine. The main function runs concurrently with it.

The final sleep above is only a demonstration; in real applications, use synchronization such as a `sync.WaitGroup` to wait for goroutines reliably.

### Proper synchronization with WaitGroup

```go

package main

import (
    "fmt"
    "sync"
)

func worker(id int, wg *sync.WaitGroup) {
    defer wg.Done()
    fmt.Println("Worker", id)
}

func main() {
    var wg sync.WaitGroup

    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go worker(i, &wg)
    }

    wg.Wait()
    fmt.Println("All workers finished")
}
```

* `Add(1)` registers a task.

* `Done()` marks the task complete.

* `Wait()` waits until the counter reaches zero.

The worker output order can vary because the goroutines execute concurrently.

### 23\. Channels

Channels communicate values between goroutines.

```go

package main

import "fmt"

func worker(ch chan string) {
    ch <- "Task completed"
}

func main() {
    ch := make(chan string)

    go worker(ch)

    message := <-ch
    fmt.Println(message)
}
```

* `ch <- value` sends a value.

* `value := <-ch` receives a value.

* `make(chan string)` creates an unbuffered channel.

An unbuffered send waits until another goroutine is ready to receive, and vice versa.

### Buffered channels

```go
ch := make(chan int, 2)

ch <- 10
ch <- 20

fmt.Println(<-ch)
fmt.Println(<-ch)
```

This channel can hold two values without an immediate receiver.

### Channel directions

```go
func send(ch chan<- int) {
    ch <- 10
}

func receive(ch <-chan int) {
    fmt.Println(<-ch)
}
```

Directional types restrict a function to sending or receiving, improving clarity and compile-time safety.

### 24\. Select statement

`select` lets a goroutine wait on multiple channel operations.

```go
select {
case message := <-ch1:
    fmt.Println(message)

case value := <-ch2:
    fmt.Println(value)

default:
    fmt.Println("No channel ready")
}
```

Without `default`, `select` blocks until one of its cases can proceed. If multiple cases are ready, Go selects one of them pseudo-randomly.

### Common uses

* Coordinating background tasks.

* Handling timeouts.

* Receiving results from multiple workers.

* Cancelling long-running operations.

### 25\. Mutexes and race conditions

A race condition occurs when concurrent execution accesses shared state unsafely, with at least one conflicting access being a write.

```go
var (
    counter int
    mu      sync.Mutex
)

func increment() {
    mu.Lock()
    counter++
    mu.Unlock()
}
```

`sync.Mutex` ensures only one goroutine at a time can execute the protected critical section.

Always release locks safely, often with `defer mu.Unlock()` after a successful lock. Keep critical sections short.

Check for data races with:

```bash
go run -race .
go test -race ./...
```

Channels are useful for communicating between goroutines; mutexes are often useful for protecting shared data. Both are valid tools.

### 26\. Context and cancellation

The `context` package lets you propagate cancellation signals, deadlines, and request-scoped values across API calls and background operations.

This is particularly important in backend services.

```go

package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    ctx, cancel := context.WithTimeout(
        context.Background(),
        2*time.Second,
    )
    defer cancel()

    select {
    case <-time.After(5 * time.Second):
        fmt.Println("Operation finished")
    case <-ctx.Done():
        fmt.Println("Cancelled:", ctx.Err())
    }
}
```

The program prints a deadline error after approximately two seconds.

In real applications, pass `ctx` into database queries, HTTP requests, and other operations that support cancellation. A context timeout only cancels operations that actually observe the context.

### 27\. Worker pools

A worker pool limits how many tasks execute concurrently. This is useful for processing files, jobs, requests, or background workloads.

```go

package main

import (
    "fmt"
    "sync"
)

func worker(id int, jobs <-chan int, wg *sync.WaitGroup) {
    defer wg.Done()

    for job := range jobs {
        fmt.Printf("Worker %d processed job %d\n", id, job)
    }
}

func main() {
    jobs := make(chan int)
    var wg sync.WaitGroup

    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go worker(i, jobs, &wg)
    }

    for job := 1; job <= 10; job++ {
        jobs <- job
    }

    close(jobs)
    wg.Wait()
}
```

What happens?

* Three workers start.

* The main goroutine sends ten jobs.

* Each worker receives and processes jobs.

* Closing `jobs` tells the workers there are no more jobs.

* `wg.Wait()` waits for all workers to finish.

This is a foundational pattern for concurrent Go applications.

### 28\. Testing in Go

Go has a built-in testing framework.

Create `math.go`:

```go
package main

func Add(a, b int) int {
    return a + b
}
```

Create `math_test.go`:

```go
package main

import "testing"

func TestAdd(t *testing.T) {
    got := Add(2, 3)
    want := 5

    if got != want {
        t.Fatalf("got %d, want %d", got, want)
    }
}
```

Run:

```bash
go test ./...
go test -v ./...
go test -cover ./...
```

Go test functions start with `Test` and accept `*testing.T`.

Advanced testing features include table-driven tests, benchmarks, fuzzing, and example tests.

### Table-driven tests

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        a, b, want int
    }{
        {1, 2, 3},
        {5, 5, 10},
        {-1, 1, 0},
    }

    for _, tt := range tests {
        got := Add(tt.a, tt.b)
        if got != tt.want {
            t.Errorf("Add(%d, %d) = %d; want %d",
                tt.a, tt.b, got, tt.want)
        }
    }
}
```

### 29\. HTTP server and REST API

Go's standard library provides `net/http`, so you can build a basic REST API without a web framework.

```go

package main

import (
    "encoding/json"
    "log"
    "net/http"
)

type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

func getUser(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")

    user := User{
        ID:   1,
        Name: "Amit",
    }

    if err := json.NewEncoder(w).Encode(user); err != nil {
        log.Println(err)
    }
}

func main() {
    http.HandleFunc("GET /users", getUser)

    log.Println("Server running on :8080")
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

Run:

```bash
go run .
```

Test it:

```bash
curl http://localhost:8080/users
```

Example response:

```json
{"id":1,"name":"Amit"}
```

This example uses method-aware routing available in modern Go versions.

### How a REST API works

Client

Browser, frontend or curl

HTTP router

Matches method and path

Handler / service

Validates input and applies business rules

Repository / database

Reads or writes persistent data

In a production application, separate handlers, business logic, and database access into distinct packages. Add request validation, timeouts, appropriate HTTP status codes, and structured logging.

### 30\. PostgreSQL database access

Go can connect to PostgreSQL using a driver such as `github.com/jackc/pgx` or through `database/sql` with a compatible driver.

A common architecture is:

* Handler: receives the HTTP request.

* Service: implements business rules.

* Repository: executes database queries.

* PostgreSQL: stores persistent data.

Using `database/sql`, a query follows this general pattern:

```go
rows, err := db.QueryContext(
    ctx,
    "SELECT id, name FROM users WHERE age >= $1",
    18,
)
if err != nil {
    return err
}
defer rows.Close()

for rows.Next() {
    var id int
    var name string

    if err := rows.Scan(&id, &name); err != nil {
        return err
    }

    fmt.Println(id, name)
}

if err := rows.Err(); err != nil {
    return err
}
```

This snippet assumes `db` is an initialized `*sql.DB`, and `ctx` is a valid context.

Use parameterized queries rather than concatenating user input into SQL. For PostgreSQL, `$1`, `$2`, and similar placeholders are typical.

### 31\. Advanced language features to learn

Once you are comfortable with the fundamentals, study these topics in order.

| Topic                 | What to understand                     |
| --------------------- | -------------------------------------- |
| defer                 | Cleanup and execution order            |
| Reflection            | Inspecting types and values at runtime |
| Embedding             | Composition and method promotion       |
| Generics              | Reusable typed algorithms              |
| sync.Once             | One-time initialization                |
| sync.RWMutex          | Multiple readers and exclusive writers |
| sync/atomic           | Atomic operations                      |
| errgroup              | Coordinating concurrent tasks          |
| io.Reader / io.Writer | Composable streaming APIs              |
| Garbage collection    | Allocation and memory management       |
| Escape analysis       | When values may need heap allocation   |
| Profiling             | CPU, memory and execution bottlenecks  |
| Race detection        | Finding unsafe concurrent access       |

Useful commands:

```bash
go vet ./...
go test -race ./...
go test -bench=. ./...
go test -fuzz=Fuzz ./...
go build ./...
```

For a particular benchmark or fuzz test, follow the test function's naming convention and select the relevant package and test flags.

### Reflection example

```go
import (
    "fmt"
    "reflect"
)

func main() {
    value := 42

    fmt.Println(reflect.TypeOf(value))  // int
    fmt.Println(reflect.ValueOf(value)) // 42
}
```

Reflection is useful for certain serializers, generic frameworks, and tooling, but prefer ordinary interfaces and type-safe code when they solve the problem more simply.

### Escape analysis

```bash
go build -gcflags="-m" .
```

The compiler reports optimization decisions, including values that escape to the heap. Don't assume every pointer is allocated on the heap; allocation depends on compiler analysis and program structure.

### 32\. Build terminal applications with Go Bubble Tea

Since you're interested in terminal interfaces, Go is particularly interesting because of the Bubble Tea framework. It uses an architecture called The Elm Architecture.

![Developing a terminal UI in Go with Bubble Tea](https://images.openai.com/static-rsc-4/BvOr54bDSnjOp8O6ZUWLcaVViaaSY0W-sjLs3MSLUE7M4YSGEZHaUNGpOwgC5vaUaT-YnemOTPBLEUOLxYePodPIul-eKF9RMBS9VUwdsDGvqJkX166p4LZl7Mqsow3oNClWoDELwaa_qPY1Wd1_Q-QgrY2Q_NggwqpccmP4ewnTW35JBfcRcK3QD2ZLcAk-?purpose=fullsize)

![推荐一个可用于快速创建 TUI 应用的框架 - Bubble Tea_golang tui-CSDN博客](https://images.openai.com/static-rsc-4/7m-rFNRF_wgz99i4200DgejmDkQ39rwfDXOg8EiqJUuRiqpt7VyXRBaETJh4Bnfuwu7Whv3Q3wwUo60cGw470uPsgzvj8VseZufWtX8hkIFKlNj4-cXmFoADOxjcDcEKuVXy4Sw_KIJOAecPkyS63kKoc3RHn74qXkWVH5--gv5DAfIc3hv09ieH7OaGg6kJ?purpose=fullsize)

![TFE - Terminal File Explorer | GGPrompts Portfolio](https://images.openai.com/static-rsc-4/hxEgQkgakF8H3rZq-VAdgPdSwSMY7Ndgb31UH2vi6g2PtfzxkKVzbmNV2NcCpw6U1Hi6KAhLWRFqOAHtOt97LcX2ThmbseYSLueEoXFZO6pNJzs79HfyNr6j_WqKH9UTVgCy8nQZObgjQdrgphCW4k0v-BI-J0iTboqziJD9Zpxyu_0fDuM69y_iHSKh77Rz?purpose=fullsize)

The architecture has three main parts:

* Model: stores the application state.

* Update: processes keyboard input and changes state.

* View: renders the terminal UI.

Install Bubble Tea:

```bash
go get github.com/charmbracelet/bubbletea
go get github.com/charmbracelet/lipgloss
```

`bubbletea` handles the event loop and keyboard input; `lipgloss` handles colors, borders, spacing, and styling.

### Minimal Bubble Tea application

Create `main.go`:

```go

package main

import (
    "fmt"
    "os"

    tea "github.com/charmbracelet/bubbletea"
)

type model struct {
    count int
}

func (m model) Init() tea.Cmd {
    return nil
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
    switch msg := msg.(type) {
    case tea.KeyMsg:
        switch msg.String() {
        case "q", "ctrl+c":
            return m, tea.Quit
        case "up":
            m.count++
        case "down":
            m.count--
        }
    }

    return m, nil
}

func (m model) View() string {
    return fmt.Sprintf(
        "Counter: %d\n↑ increase  ↓ decrease\nq: quit\n",
        m.count,
    )
}

func main() {
    p := tea.NewProgram(model{})

    if _, err := p.Run(); err != nil {
        fmt.Println("Error:", err)
        os.Exit(1)
    }
}
```

Run:

```bash
go mod tidy
go run .
```

Use the up and down arrow keys to change the counter, and `q` to quit.

### Your advanced TUI project

Build a terminal application with these features:

* Dark purple theme and rounded borders.

* Search box with fuzzy filtering.

* Selectable list with highlighted rows.

* Keyboard navigation using arrow keys.

* Item counter and status bar.

* File picker and file preview.

* Asynchronous loading using goroutines and Bubble Tea commands.

* Configuration stored in a JSON file.

Suggested libraries:

* Bubble Tea — application state and event loop.

* Lip Gloss — colors, borders and layout.

* Bubbles — text inputs, lists, spinners and viewports.

* Fuzzy — fuzzy matching and search.

Official resources: [Bubble Tea](https://github.com/charmbracelet/bubbletea), [Lip Gloss](https://github.com/charmbracelet/lipgloss), [Bubbles](https://github.com/charmbracelet/bubbles).

### 33\. Complete project-based learning plan

Build these projects in order. Each one reinforces concepts from earlier sections.

### Project checklist

0 of 8 completed

1\. Calculator CLI

Beginner · Variables, functions, switch, errors

2\. Expense tracker

Beginner · Structs, slices, JSON, file handling

3\. Contact manager

Beginner+ · Maps, pointers, packages, CRUD

4\. Concurrent URL checker

Intermediate · HTTP client, goroutines, channels, worker pools

5\. REST API with PostgreSQL

Intermediate · HTTP, SQL, context, validation, testing

6\. Terminal fuzzy finder

Intermediate+ · Bubble Tea, Lip Gloss, keyboard events

7\. Concurrent job queue

Advanced · Cancellation, synchronization, retries, backpressure

8\. Production-ready API

Advanced · Authentication, Docker, observability, CI/CD

### 34\. Practice questions

Try these without looking at solutions first.

1. What is the difference between an array and a slice?

They are identical

An array has fixed length; a slice is a flexible view over an array

A slice can store only strings

2. What does the go keyword do?

Compiles the program

Starts a goroutine

Creates a new package

3. How do you check for a normal error returned by a Go function?

catch (err)

if err != nil

if err == false

4. What is the purpose of a channel?

Store database tables

Communicate values between goroutines

Replace all functions

5. How does a Go type satisfy an interface?

By declaring extends

By registering with the compiler

By implementing the interface's required methods

Reset quiz

### 35\. Recommended learning resources

* [Official Go Tour](https://go.dev/tour/) — interactive fundamentals.

* [Go by Example](https://gobyexample.com/) — short runnable examples.

* [Effective Go](https://go.dev/doc/effective_go) — idiomatic coding style.

* [Go standard library](https://pkg.go.dev/std) — packages and API documentation.

* [Go blog](https://go.dev/blog/) — deeper language and concurrency topics.

