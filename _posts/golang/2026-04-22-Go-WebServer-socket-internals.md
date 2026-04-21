---
title: "The Internal working of Go Web programming-Part 2"
date: 2026-04-22
categories: [golang]
classes: wide
tags: [http, webserver, golang]
excerpt: "Get to know the details about most commony used Go web server pattern"
---

In the First part of this article , we learned about how the web server pattern fnctions using the net/http package. 
In this post we explor the aspects linked with the tcp socket programming which is mostly hidden behind the scenes of this line
```bash
http.ListenAndServe(":8080", nil)
```
This one lines hides so many details like:
- opening a network port
- waiting for connections
- accepting browsers/users
- parsing HTTP
- spawning goroutines
- calling your handlers
- sending responses concurrently

If we have to summarize the overall process, it would look like this sequence
```text
Your app starts
↓
Go opens port 8080
↓
Waits forever for incoming users
↓
User connects
↓
Go creates goroutine
↓
Runs handler
↓
Responds
↓
Keeps listening for next user
```
Lets decode it step by step


### What is port 8080

When we write :8080, it means to listen on network port 8080 on the machine where the program is running.
Think of it like if ip address is the street name, port is the house number on that street.

### What does ListenAndServe mean

**Listen** means wait for incoming TCP connections
**Serve** means once a connection arrives, handle the HTTP requests.
Conceptually Go does something like:
```bash
listener := net.Listen("tcp", ":8080")

for {
    conn := listener.Accept()
    go handleConnection(conn)
}
```
Through this piece of code, Go ask the OS to reserve port 8080 for this program.
If that is successful, then no other app can use it till this application is alive.
If not, you will get the error 
```text
bind: address already in use
```
which means another app is already using and owning this port.
Then Go, waits in an infinite loop for incoming connections.
It is a blocking call. when the user visits
```text
http://localhost:8080/
```
the browser opens a TCP connection and Go receives a new client.
As a next step, Go starts a **Goroutine** (Its like a thread in other programming languages)
```bash
go handleConnection(conn)
```
The Keyword *go* makes the handleConnection function a goroutine. 
This handles the user in background, while the main listener keeps accepting other.
This also means that one slow user will not block everyone else. 
Imagine 100 úsers visiting the site simultaneously, Go can create that many goroutines.
```text
User 1 -> goroutine A
User 2 -> goroutine B
User 3 -> goroutine C
...
```

Then the next steps are what has been explained in the part-1, but i will briefly summarize them here for you.

#### Parse HTTP Request

Inside connection handler, Go reads
```bash
GET /products HTTP/1.1
Host: ...
Headers...
```
builds r *http.Request and then finds matching route.

#### Call your handler

If you registered 
```bash
http.HandleFunc("/products", productsHandler)
```
Go executes
```bash
productsHandler(w, r)
```
inside goroutine.

#### Write Response

```bash
fmt.Fprintf(w, "Products page")
```
Go sends bytes back through connection. Browser renders the page. 
Depending on the connection settings, HTTP/1.1 often keeps the connection alive for reuse. Otherwise closes after response.

### Why nil in ListenAndServe

The second argument of ListenAndServe is the handler/router. nil here means that we use Go's default router *DefaultServeMux*
This could have been written like this
```bash
mux := http.NewServeMux()
mux.HandleFunc("/", home)

http.ListenAndServe(":8080", mux)
```

### Code example

```bash
package main

import (
    "fmt"
    "net/http"
)

func home(w http.ResponseWriter, r *http.Request) {
    fmt.Println("Request received")
    fmt.Fprintf(w, "Hello User")
}

func main() {
    fmt.Println("Server running on :8080")
    http.HandleFunc("/", home)
    http.ListenAndServe(":8080", nil)
    if err != nil {
        log.Fatal(err)
    }
}
```
