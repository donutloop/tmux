[![Build Status](https://travis-ci.org/donutloop/trixie.svg?branch=master)](https://travis-ci.org/donutloop/trixie)

# What is trixie (Tree multiplexer)? 

trixie is a lightweight very fast HTTP request router for Go 1.7+.

The difference between the default mux of Go's net/http package and this mux is, it's supports variables and regex in the routing pattern and matches against the request method. It also based on a tree.

## Handler 

The handler is a simple standard http.Handler 

```go 
func(w http.ResponseWriter, r *http.Request) 
```

## Routing Rules

Some examples of valid URL patterns are:

* `/post/all`
* `/post/:number`
* `/post/:number/page/:number`
* `/post/:string`
* `/images/#([0-9]{1,})`
* `/favicon.ico`
* `/:string/:string/:number/:number`

* Parameter elements starting with : indicate a parameter segment in the path.
* Regex elements starting with # indicate a regex segment in the path.

## Routing Priority

The priority rules in the router are simple.

* A regex segment has the highest priority
* A parameter Segment has middle priority
* A static path segment has the lowest priority.

For Instance:

```go 
router.GET("/#([0-9]{1,})/post", handler) // highest priority
router.GET("/:string/post", handler) // middle priority
router.GET("/home/post", handler) // lowest priority
```

## Example (Method GET & Regex):

```go
 package main
 
 import (
         "fmt"
         "github.com/donutloop/trixie"
         "net/http"
 )
 
 func main() {
         r := trixie.Classic()
         //URL: http://localhost:8080/user/1
         r.Get("/user/#([0-9]){1,}", userHandler)
         if err := http.ListenAndServe(":81", r); err != nil {
                 fmt.Println(err)
         }
 }
 
 func userHandler(rw http.ResponseWriter, req *http.Request) {
         param := trixie.GetRouteParameters(req) // value of regex segment
         rw.Write([]byte(param["seg1"]))
 }
    
 ```   