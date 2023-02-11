# go

- strongly typed

## package main

- https://pkg.go.dev/
- all files will be part of a package - compiled to a exe
- fmt package for standard functions
- func main(){} single main() function
- shell `go run main.go`
- shell make executable `go build main.go`

## variables

- strings double quotes
- nameFour := "yoshi" shorthand syntax can only be used inside function on declaration

## arrays / slices

### arrays

- arrays are fixed length
- zero indexed

var ages [3]int = [3]int{20,25,30}
same as
var ages = [3]int{20,25,30}

-length is `len(ages)`

names[1] - get item at names[1] position 1 (zero indexed)
names[1]='luigi'

### slices

- dont have to define size
- you can append() to a slice (not to an array) - returns a new slice (then you have to assign again)

```go
car scores = []int{20,25,30}
scores[2] = 25;
scores = append(scores, 85)
```

- getting segments of slice

```go
rangeOne := names[1:3] (does not include item at position 3)
```

- getting everything in slice from index onwards

```go
rangeOne := names[2:]
```

-get from start up to (but not including 3)

```go
rangeThree := names[:3]
```

## forloop

```go
func main(){
  // x:=0
  // for x < 5{
  //   fmt.Printlin("value of x is: ", x)
  //   x++
  // }

  for i:=0; i<5 ; i++{
    fmt.Printlin("value of x is: ", i)
  }

//for..in using placeholder variables
  for index, value:= range names {
    fmt.Printf("the value at index %v is %v \n", index, value)
  }

  //for..in using placeholder variables (only using value)
  //value is local to the for-loop
  for _, value:= range names {
    fmt.Printf("the value at index %v is %v \n", value)
  }
}
```

### ifelse

```go
if age < 30 {

}else if age < 40 {

}else{

}
```

### packages

- files with same package declaration can gain access to variables/functions inside these files (if declared at top level) BUT when you run the main.go file you have to also include the file you are using

```shell
go run main.go greetings.go
```

### map

- keys have to be same type / values have to be same type
- eg. declare map of keys of string containing float64

```go
func main(){
  menu:=map[string]float64{
    "soup": 4,
  }
}
```

## pass by value

### Non pointer values

- Group A - String, Ints, Floats, Booleans, Arrays, Structs (DOESNT CHANGE ORIGINAL VALUE)

### Pointer wrapper values

- Group B - Slices, Maps, Functions (CHANGES ORIGINAL VALUE AS COPIED VALUE POINTS TO A POINTER THAT POINTS TO SAME VALUE IN MEMORY)

- when calling a function you are passing a copy of the original value - and updating the copy
- solution is to return a value from the function and assign to a variable

## Pointers

- pointers point to a memory location
- use & in front of variable
- use \* in front of pointer to get the value that the pointer points to

## structs

- similar to classes or type in typescript

```go
package main

type bill struct {
  name string
  items map[string]float64
  tip float64
}

func newBill (name string) bill {
  b:= bill{
    name: name,
    items: map[string]float64{},
    tip:0,
  }
  return b
}

```

```go
// main.go
package main
import "fmt"

func main(){
  mybill := newBill("marios bill")

  fmt.Println(mybill)
}

```

## receiver functions

- limiting functions to proto like functions to work with only objects of struct
- you can add spacing (fixed reserve to a string) using: %-25
- when you have these receiver functions - they also use a copy of the data unless you use a pointer (then you dont need to use \* to reference the data, Go automatically does this)

```go

func main(){
  mybill := newBill("marios bill")
  fmt.Println(mybill.format())
}
```

```go
func newBill(name string)bill{
  b:=bill {
    name: name,
    items: map[string]float64{"pie": 5.99, "cake":3.99},
    tip: 0
  }
}


//this is the magic part (b bill) says this function format() is only applicable to bill structs which we name b
func (b bill) format() string {
  fs:="bill breakdown:\n"
  var total float64 = 0

  //list items
  for k, v := range b.items {
    // reserve 25 spacing for placeholder %v
    fs += fmt.Sprintf("%-25v ...$%v \n", k+":", v)
    total += v
  }

  //total
  // reserve 25 spacing for placeholder %v
  fs += fmt.Sprintf("%-25v ...$%0.2f", "total:", total)

  return fs
}


```

## user input / switch statements

//reading from os.Stdin

- bufio library -bufio.NewReader(os.Stdin)
- os library

```go
import (
  "fmt"
  "bufio"
  "os"
  "strings"
)

//receives user input from user
func getInput(prompt string, r *bufio.Reader) (string, error){
  fmt.Print(prompt)
  input, err := r.ReadString('\n')  // readin after which character user types
  return strings.TrimSpace(input), err   //trim whitesapce
}

func createBill() bill {
  reader := bufio.NewReader(os.Stdin)

  name, _ := getInput("create a new bill name: ", reader)

  b := newBill(name)
  fmt.Println("created the bill - ", b.name)

  return b
}

func promptOptions(b bill){
  reader := bufio.NewReader(os.Stdin)
  opt, _:= getInput("Choose option (a - add item, s - save bill, t - add tip):", reader)
  //switch
  switch opt {
    case "a":
      name, _ := getInput("Item name: ", reader)
      price, _ := getInput("Item price: ", reader)
      fmt.Println('a')
    case "s":
      fmt.Println('s')
      b.save()
    case "t":
      tip, _ := getInput("Enter tip amount ($): ", reader)
      fmt.Println('t')
    default:
      fmt.Println("that was not a valid option...")
      promptOptions(b)
  }
}

func main(){
  mybill := createBill()
  promptOptions(mybill)
}
```

## parsing floats

- reading input from user returns a string
- "strconv" package

```go
p, err := strconv.ParseFloat(price, 64)
if err != nill {
  fmt.Println("the price must be a number")
  promptOptions(b)
}

b.addItem(name, p)
fmt.Println("item added - ", name, price)
  promptOptions(b)


```

## saving text to file

```go
func (b *bill) save(){

  //turning string into byte slice
  data :=[]byte(b.format())
  err := os.WriteFile("bills/"+b.name + ".txt", data, 0644)
  if err != nil{
    panic(err)  //interupt flow of app
  }
  fmt.Println("bill was saved to file")
}
```

## interfaces

- define interface - then use a general function that can take in the interface as a type when an instance adheres to the interface

```go
type shape interface {
  area() float64
  circumf() float64
}

func printShapeInfo(s shape){
}
```
