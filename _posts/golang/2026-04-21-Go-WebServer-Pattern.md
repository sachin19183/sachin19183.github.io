---
title: "The Internal working of Go Web programming"
date: 2026-04-21
categories: [golang]
classes: wide
tags: [http, webserver, golang]
excerpt: "Get to know the details about most commony used Go web server pattern"
---

## Decode the http.HandleFunc() pattern 


```bash
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {})
```
As a newbee in Go, when i first came across this function, i thought what in the world is this.  
In this post, i have tried to simplify it for those readers who are in the same place as i was.
This function call means that when someone visits **/** on the web server, run this function to handle the request.
In other words, for homepage requests, run this function. Use w to reply and r to inspect the request.

It is an example of Go's anonymous function. They are functions without a name. 
They are often created inline where you need them, instead of defining a separate named function.

```bash
func(a int, b int) int {
    return a + b
}
```
But writing it alone does not results in its execution.  You either assign it to a variable liek this:

```bash
greet := func(name string) {
    fmt.Println("Hello", name)
}

greet("Alice")
```

or invoke immediately like this:

```bash
func() {
    fmt.Println("Runs immediately")
}()
```
This is called an Immediately Invoked Function Expression. 
The anonymous functions are commonly user in goroutines.

Comming back to our topic,  What we are doing in this http.HandleFunc is that we are registering a route (URL path) and attaching code to run when that route is hit.

Lets discuss each component of this function call:

### http

This is Go's built in package and provides tools for web servers, HTTP requests, Responses and routing.

to use it we need to import it in our code like this:
```bash
import "net/http"
```

### HandleFunc

This function is part of the net/http package. It expects a URL pattern and a handler function.
```bash
func HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```

### "/"

This is the route path which means the homepage or the root url in this context. This could get resolve to 
- http://localhost:8080/ or your website homepage url :
- http://thesilentwarrior.org/

### The Anonymous function

```bash
func(w http.ResponseWriter, r *http.Request) {}
```
This code is executed whenever someone visits **/** .
It takes two  parameters:
- w http.ResponseWriter   Which lets you send data back to the browser. So lets say we write "Hello world" to yhe channel **w**.
  Then the browser will receive this message
  ```bash
  fmt.Fprintf(w, "Hello user")
  ```

 - r *http.Request         This contains incoming request data. for example:
     - url path
     - query params
     - headers
     - cookies
     - method( GET/POST)
     - body


### Code Example

```bash
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Welcome Home!")
    })

    http.ListenAndServe(":8080", nil)
}
```

 When you visit http://localhost:8080/ the browser would show **Welcome Home!**
Think of it as 
```bash
routes["/"] = this function
```
when the request comes
```bash
GET /
```
Go checks route table:
```bash
"/" => run this handler
```

 If we had not use the Anonymous function here, the code would have been like this.

 ```bash
func homeHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Welcome")
}

http.HandleFunc("/", homeHandler)
```

The benefit of using anonymous function is that because the route handler logic is small and tied to one route. 
This is the perfect use case for Go's anonymous function.

This can be used for any url path. for example
```bash
http.HandleFunc("/about", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "About Page")
})
```
Here we use this function for the /about url of our web server.


## Understanding the HTTP Method

Lets understand the basics of http method to get a complete picture on the web programming using golang. 
pls note that these concepts are applicable to any programming language.

When a browser or client sends a request, it sends url and method. The common methods used are:
- GET
- POST
- PUT
- DELETE
- PATCH

HTTP uses **ROUTE PATH + METHOD** together and not just the route PATH.
Route  matches URL path but request method tells what operation user wants
If we look at simple pages like /home, /about, they dont need multiple methods as they are simply used to display content.
However in pages like login, the same url path can be used for different purposes or intent. 

- If we pass GET /login , that would mean that the intetn is to show the login page.
- If we pass POST /login, the intent would be to submit the login form data.

So same route, different behaviour.
That is why it is important that we implement the method check logic inside the http.HandleFunc. 
If we dont do that, all method would result in the same behaviour.

```bash
http.HandleFunc("/login", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello")
})
```
would result in 
```text
GET /login → Hello
POST /login → Hello
DELETE /login → Hello
```

Better practise is to use this template

```bash
http.HandleFunc("/login", func(w http.ResponseWriter, r *http.Request) {
    if r.Method == "GET" {
        fmt.Fprintf(w, "Login page")
        return
    }

    if r.Method == "POST" {
        fmt.Fprintf(w, "Processing login")
        return
    }

    http.Error(w, "Method Not Allowed", http.StatusMethodNotAllowed)
})
```
The r.method helps in doing that. The advantage here is that we can use the same url /login to implement different behaviors.  In real api scenario, the strucutre of methods looks like this.
```text
GET /products        -> list products
POST /products       -> create product
PUT /products/10     -> update product 10
DELETE /products/10  -> delete product 10
```


## How browser clicking a form button becomes POST /login 

When you click the form button, it translates to r.Method == "POST"
```text
User fills form
↓
Clicks Submit
↓
Browser creates HTTP request
↓
Sends request to Go server
↓
Go matches route
↓
Handler runs
↓
Your code reads r.Method / form data
↓
Response sent back
```

### Browser HTML Form

```bash
<form action="/login" method="POST">
  <input name="username">
  <input name="password" type="password">
  <button type="submit">Login</button>
</form>
```
Lets take this example for the login page. This tells the browser to:
- send data to /login
- use POST
- include form fields

When the user clicks the submit button after filling the values for username and password, browser prepares a netwrk request.
```bash
POST /login HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=raj&password=secret123
```
Lets decode this request

#### Top Line POST /login HTTP/1.1

This means the Method used is POST
The Path is /login
The protocol used is HTTP/1.1

within GO it translates to 
```bash
r.Method   // POST
r.URL.Path // /login
```

#### Headers

```text
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
```
This is the metadata abot the request and is translated to r.Header in Go.

#### Body
```text
username=raj&password=secret123
```
This is the actual submitted form data. Pls note that the data is part of the Body.
A general sugestion for new developers is to always use POST for :
- passwords
- forms
- creation actions
  This is because sensitive data shoul not go in the URL. Had we used the GEt method here then the browser would have sent this:
```bash
GET /login?username=raj&password=secret123
```

In general GET method should be used for fetching page/data only.

#### Request reaches the Go server

```text
http.ListenAndServe(":8080", nil)
```
The go server listens on port 8080 and waits for the incoming request once this request reaches, it matches the path

```bash
http.HandleFunc("/login", loginHandler)
```
As its a direct match, it executes the corresponding code
```bash
func loginHandler(w http.ResponseWriter, r *http.Request) {
```
Go passes:
- w -> response writer
- r -> request object

Then the code check method gets invoked
```bash
if r.Method == "POST" {
```
now comes the logic for reading the form data:
```bash
r.ParseForm()

username := r.FormValue("username")
password := r.FormValue("password")
```
On execution of these lines, the variables username will contain raj and password will contain secret123
Here is the complete code sample
```bash
package main

import (
    "fmt"
    "net/http"
)

func loginHandler(w http.ResponseWriter, r *http.Request) {
    if r.Method == "GET" {
        fmt.Fprintf(w, `
            <form action="/login" method="POST">
                <input name="username">
                <input name="password" type="password">
                <button type="submit">Login</button>
            </form>
        `)
        return
    }

    if r.Method == "POST" {
        r.ParseForm()

        username := r.FormValue("username")

        fmt.Fprintf(w, "Welcome %s", username)
        return
    }
}

func main() {
    http.HandleFunc("/login", loginHandler)
    http.ListenAndServe(":8080", nil)
}
```


## Understanding the http.ResponseWriter

Inside the handler function
```bash
func(w http.ResponseWriter, r *http.Request)
```
w is the connection back to the browser. So when broweser asks for something , go gives you a pipe to reply through. 
ResponseWriter is that pipe.
Lets take an example where you need to write "Hello World" in the HTTP response towars the browser.
Browser sends the request GET /hello
```bash
http.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World")
})
```
Here fmt.Fprintf() calls w.Write() internally. This could have been replaced by
```bash
w.Write([]byte("Hello World"))
```
Now the Go server builds the HTTP response
```bash
HTTP/1.1 200 OK
Content-Length: 11
Content-Type: text/plain; charset=utf-8

Hello World
```
Browser reads the response body and renders it on the screen.

### Internally ResponseWriter is an Interface

```bash
type ResponseWriter interface {
    Header() Header
    Write([]byte) (int, error)
    WriteHeader(statusCode int)
}
```
It provides methods to 
- set headers
- write nody
- set status code

An example code looks like this
```bash
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "text/html")
    fmt.Fprintf(w, "<h1>Welcome</h1><p>This is home page</p>")
})
```

#### Set Headers

```bash
w.Header().Set("Content-Type", "text/html")
fmt.Fprintf(w, "<h1>Hello</h1>")
```
Without proper header, the text "Hello" would display as plain text.

You can also set a status code lets say for a page not found error like this:
```bash
w.WriteHeader(http.StatusNotFound)
fmt.Fprintf(w, "Page not found")
```

### Internal flow

When the request comes in, a browser tcp connection is opened. Go creates an object tied to that connection. 
When w.Write() is invoked, Go sends bytes through the socket to browser.
That is why the order matters here. 
```bash
w.Header().Set(...)
w.WriteHeader(...)
w.Write(...)
```
because once body starts writing (w.Write), some headers may have already be sent.
```bash
fmt.Fprintf(w, "Hello")
w.WriteHeader(404)
```
would result in mistake as body has already started sending while we set the header as 404. The correct order is 
```bash
w.WriteHeader(404)
fmt.Fprintf(w, "Not found")
```


## Advice for big production grade programs

Use named functions instead of anonymous function described above as that is easier to manage, debug and troubleshoot.
Use routers like Gin, Chi, Echo instead of HandleFunc. The code looks like this for a named function
```bash
func homeHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Home")
}

http.HandleFunc("/", homeHandler)
```
the method handling looks like this in GIN
```bash
r.GET("/login", showLogin)
r.POST("/login", processLogin)
```
This becomes much cleaner then the manual if r.method
