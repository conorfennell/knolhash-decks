Q: What is the "zero value" of a slice declared as `var s []int`?
A: `nil`
C: Go/Slices/Initialization

Q: Between `var s []int` and `s := make([]int, 0)`, which results in a non-nil slice?
A: `s := make([]int, 0)`
C: Go/Slices/Initialization

Q: How does a `nil` slice (from `var s []int`) appear when encoded to JSON?
A: `null`
C: Go/Slices/JSON

Q: How does an empty, initialized slice (from `make([]int, 0)`) appear when encoded to JSON?
A: `[]`
C: Go/Slices/JSON

Q: What are the three fields contained in a Go slice header?
A: 
1. Pointer (to underlying array)
2. Length (`len`)
3. Capacity (`cap`)
C: Go/Slices/Internals

Q: In the `append` function, what happens if `len + 1 <= cap`?
A: Go writes the value into the existing array and increments the length.
C: Go/Slices/Append

Q: Why must you assign the result of `append` back to the slice (e.g., `s = append(s, x)`)?
A: Because `append` may allocate a **new underlying array** and return a header with a new pointer.
C: Go/Slices/Append

Q: How does Go typically grow the capacity of a small slice (under 256 elements) when it runs out of space?
A: It **doubles** the capacity.
C: Go/Slices/Append

Q: What is the most efficient way to initialize a slice if you know it will eventually hold `N` elements?
A: Use `make` with a pre-defined capacity: `make([]int, 0, N)`
C: Go/Slices/Performance

Q: Why is direct index assignment (`arr[i] = x`) faster than `append`?
A: It avoids the overhead of checking capacity and the logic required for potential reallocation.
C: Go/Slices/Performance

Q: What does it mean that interfaces in Go are satisfied "implicitly"?
A: A type does not need to explicitly declare that it implements an interface (e.g., no implements keyword); it satisfies the interface simply by implementing all required methods.
C: Go/Interfaces

Q: How do you lowecase strings in Go?
A: 
```go
import (
    "strings"
)
func toLowerCase(s string) string {
    return strings.ToLower(s)
}
```

Q: How do you convert an integer to a string in Go?
A: 
```go
import (
    "strconv"
)

func intToString(i int) string {
    return strconv.Itoa(i)
}
```
C: Go/Strings/Conversion

Q: How do you convert a string to an integer in Go?
A: 
```go
import (
    "strconv"
)

func stringToInt(s string) (int, error) {
    return strconv.Atoi(s)
}
```
C: Go/Strings/Conversion

Q: How do you efficiently build a string in Go?
A: Use `strings.Builder`. It minimizes memory allocations.
```go
import (
    "strings"
)

func buildString(parts []string) string {
    var sb strings.Builder
    for _, part := range parts {
        sb.WriteString(part)
    }
    return sb.String()
}
```
C: Go/Strings/Builder

Q: How do you prevent variable shadowing when initializing variables inside conditional blocks?

A: Shadowing occurs when using `:=` inside a block, which creates a **new local variable** instead of updating the outer one. To avoid this, declare the variable in the outer scope first and use the **assignment operator** (`=`) inside the block. This also allows for mutualized error handling after the block.

### **The Mistake (Shadowing)**
```go
var client *http.Client
if tracing {
    // BUG: := creates a new 'client' local to this if-block
    client, err := createClientWithTracing() 
    if err != nil { return err }
}
// client is still nil here!

```

### **The Fix (Assignment)**

```go
var client *http.Client
var err error // 1. Pre-declare

if tracing {
    client, err = createClientWithTracing() // 2. Use '='
} else {
    client, err = createDefaultClient()
}

if err != nil { // 3. Common error handling
    return err
}
// client is correctly initialized here

```
