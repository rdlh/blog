---
layout: post

title: "[English] Introduction to : Go"
cover_image: cover.png

excerpt: "A short golang introduction"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

> Go is an open source programming language designed for building simple, fast, and reliable software.

# A simple hello world

Let's do a simple hello world, include the `main` package (golang core) and import [`fmt`](https://golang.org/pkg/fmt/) which implements formatted I/O with functions analogous to C's printf and scanf.

```go
package main


import "fmt"

func main() {
    fmt.Println("hello world")
}

```

There is two ways to execute this go code, the first one is simply call the `go run` command like that:

```bash
$ go run hello-world.go

```

Sometimes we’ll want to build our programs into binaries. We can do this using `go build`, and simply execute it.

```bash
$ go build hello-world.go
$ ./hello-world

```

# Values

```go
package main


import "fmt"

func main() {
    // Strings, which can be added together with +.
    fmt.Println("Remi" + " Delhaye")

    // Integers and floats.
    fmt.Println("1+1 = ", 1+1)
    fmt.Println("7.0/3.0 = ", 7.0/3.0)

    // Booleans, with boolean operators as you’d expect.
    fmt.Println(true && false)
    fmt.Println(true || false)
    fmt.Println(!true)
}

```

#### output:

```bash
$ go run values.go
Remi Delhaye
1+1 = 2
7.0/3.0 = 2.3333333333333335
false
true
false

```

# Variables

```go
package main


import "fmt"

func main() {
    // var declares 1 or more variables.
    var a string = "initial"
    fmt.Println(a)

    // You can declare multiple variables at once.
    var b, c int = 1, 2
    fmt.Println(b, c)

    // Go will infer the type of initialized variables.
    var d = true
    fmt.Println(d)

    // Variables declared without a corresponding initialization are zero-valued. For example, the zero value for an int is 0.
    var e int
    fmt.Println(e)

    // The := syntax is shorthand for declaring and initializing a variable, e.g. for var f string = "short" in this case.
    f := "short"
    fmt.Println(f)
}

```

#### output:

```bash
$ go run variables.go
initial
1 2
true
0
short

```

# For statement

```go
package main


import "fmt"

func main() {
    // The most basic type, with a single condition.
    i := 1
    for i <= 3 {
        fmt.Println(i)
        i = i + 1
    }

    // A classic initial/condition/after for loop.
    for j := 7; j <= 9; j++ {
        fmt.Println(j)
    }

    // for without a condition will loop repeatedly until you break out of the loop or return from the enclosing function.
    for {
        fmt.Println("loop")
        break
    }
}

```

#### output:

```bash
$ go run for.go
1
2
3
7
8
9
loop

```

# If/Else statement

```go
package main


import "fmt"

func main() {
    // Here’s a basic example.
    if 7%2 == 0 {
        fmt.Println("7 is even")
    } else {
        fmt.Println("7 is odd")
    }

    // You can have an if statement without an else.
    if 8%4 == 0 {
        fmt.Println("8 is divisible by 4")
    }

    // A statement can precede conditionals; any variables declared in this statement are available in all branches.
    if num := 9; num < 0 {
        fmt.Println(num, "is negative")
    } else if num < 10 {
        fmt.Println(num, "has 1 digit")
    } else {
        fmt.Println(num, "has multiple digits")
    }
}

```

> Note that you don’t need parentheses around conditions in Go, but that the braces are required.

#### output:

```bash
$ go run ifelse.go
7 is odd
8 is divisible by 4
9 has 1 digit

```

# Arrays

```go
package main


import "fmt"

func main() {
    // Here we create an array a that will hold exactly 5 ints. The type of elements and length are both part of the array’s type. By default an array is zero-valued, which for ints means 0s.
    var a [5]int
    fmt.Println("emp:", a)

    // We can set a value at an index using the array[index] = value syntax, and get a value with array[index].
    a[4] = 100
    fmt.Println("set:", a)
    fmt.Println("get:", a[4])

    // The builtin len returns the length of an array.
    fmt.Println("len:", len(a))

    // Use this syntax to declare and initialize an array in one line.
    b := [5]int{1, 2, 3, 4, 5}
    fmt.Println("dcl:", b)

    // Array types are one-dimensional, but you can compose types to build multi-dimensional data structures.
    var twoD [2][3]int
    for i := 0; i < 2; i++ {
        for j := 0; j < 3; j++ {
            twoD[i][j] = i + j
        }
    }
    fmt.Println("2d: ", twoD)
}

```

> Note that arrays appear in the form [v1 v2 v3 ...] when printed with fmt.Println.

#### output:

```bash
$ go run arrays.go
emp: [0 0 0 0 0]
set: [0 0 0 0 100]
get: 100
len: 5
dcl: [1 2 3 4 5]
2d:  [[0 1 2] [1 2 3]]

```
