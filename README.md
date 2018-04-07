![](https://u.sicp.me/m52vY.png)

# Go Glitch

A go package to glitch an image based on an expression you pass in as input!
The only limit is your creativity (and patience)!

    
# What is the deal with the expressions?

You can think of the image as a functor that you map an expression to, for each pixel's component colors,
returning a new one. The allowed operators are:

* `+` plus
* `-` minus
* `*` multiplication
* `/` division
* `%` modulo
* `&` bit and
* `|` bit or
* `:` bit and not
* `^` bit xor
* `<` bit left shift
* `>` bit right shift

The expressions are made up of operators, numbers, parenthesis, `c` (the current value of each pixel
component color), `s` (the value of each pixel's last saved evaluated expression), `n` (the luminosity,
or grayscale component of each pixel) and `r` (a pixel made up of a random color component from the
neighboring 8 pixels).

## Examples

* `128 & (c - ((c - 150 + s) > 5 < s))`
* `(c & (c ^ 55)) + 25`
* `128 & (c + 255) : (s ^ (c ^ 255)) + 25`


# Usage

```go
package main

import (
    "os"
    "time"
    "math/rand"
    "image"
    "image/png"
    _ "image/jpeg"

    "github.com/sugoiuguu/go-glitch"
)

func main() {
    f, err := os.Open(os.Args[2])
    if err != nil {
        panic(err)
    }
    defer f.Close()

    f2, err := os.Create(os.Args[1])
    if err != nil {
        panic(err)
    }
    defer f2.Close()

    img, _, err := image.Decode(f)
    if err != nil {
        panic(err)
    }

    rand.Seed(time.Now().UnixNano())

    expr, err := glitch.CompileExpression("(n|(c > 1)) ^ 128")
    if err != nil {
        panic(err)
    }

    g, err := expr.JumblePixels(img)
    if err != nil {
        panic(err)
    }
    png.Encode(f2, g)
}
```