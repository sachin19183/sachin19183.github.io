---
title: "Practical tips on Golang package usage and project structure"
date: 2026-04-06
categories: [golang]
classes: wide
tags: [modules, packages, golang]
excerpt: "Get a real understanding on how to structure your project and organize code effectively & encapsulate your logic"
---

# From “How do I use packages?” to actually understanding Go structure

So I was learning Go and I hit this point where I asked:

> *“How do I use external packages (like GitHub imports)?
> And how do people structure real-world Go projects?”*

At the time, it felt like two separate questions. Turns out… they’re very connected.

This post is basically that conversation plus what clicked for me after.

---

## 📦 First: using external packages

I expected this to be complicated. It wasn’t.

For example, if you want to use a popular HTTP framework like **Gin**, you just write:

```go
import "github.com/gin-gonic/gin"
```

And Go just… handles it.

The first time you run your code:

* it downloads the package
* adds it to `go.mod`
* caches it locally

That’s it. No `npm install`, no setup rituals.

---

### Alternate way (explicit commands)

```bash
go get github.com/gin-gonic/gin   # add dependency
go mod tidy                       # clean unused deps
go list -m all                    # list all modules
```

---

### Example I tried

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()

    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{"message": "Hello, world!"})
    })

    r.Run()
}
```

Run:

```bash
go run .
```

The first time you run it, Go:

* downloads the package
* updates `go.mod` and `go.sum`

Your `go.mod` will now include something like:

```go
require github.com/gin-gonic/gin vX.X.X
```

---

### What clicked here

Go doesn’t want you to *manage dependencies manually*.
You declare them, and Go takes care of the rest.

That part felt very clean.

---

## 🏗️ Then I asked: okay… but how do I structure a real project?

Because putting everything in `main.go` clearly isn’t it.

I found a structure like this:

```text
myapp/
  ├── go.mod
  ├── cmd/
  │    └── api/
  │         └── main.go
  ├── internal/
  │    ├── handlers/
  │    ├── services/
  │    └── models/
  ├── pkg/
  │    └── utils/
```

### What these folders generally mean:

* `cmd/` → entry points (your apps)
* `internal/` → private application logic (This actually can be used for encapsulation!!)
* `pkg/` → reusable/shared code

Typical flow:

* `main.go` → starts the app
* `handlers` → HTTP layer
* `services` → business logic
* `models` → data structures

---

## 🔐 Then I got stuck on this word: `internal/`

I thought:

> “Wait… Go already has lowercase functions for private stuff.
> So why do we need `internal/`?”

---

## 🔡 First thing I learned (this surprised me)

Lowercase functions are **only private to the package**, not the whole project.

```go
package services

func CreateUser() {}   // exported
func validateUser() {} // not exported
```

From another package:

```go
services.CreateUser()   // ✅ works
services.validateUser() // ❌ error
```

Even though both are in the same project.

That’s when it clicked:

> lowercase ≠ project-private
> it’s just package-private

---

## 🤯 Then came the confusing part

> “Other packages in your module still can’t access it
> but without `internal/`, other projects can still import your package”

This sounded contradictory at first.

---

## 🧠 Let’s break this properly (this is the key part)

There are **two completely different rules**:

---

### 🔹 Rule 1: Lowercase (inside your project)

Controls **what other packages in your project can access**

* `validateUser()` → ❌ not accessible outside its package
* even within the same module

👉 This is **package-level privacy**

---

### 🔹 Rule 2: `internal/` (outside your project)

Controls **who can import your package**

#### Case A — without `internal/`

```text
myapp/services/
```

Another project can do:

```go
import "myapp/services" // ✅ allowed
```

---

#### Case B — with `internal/`

```text
myapp/internal/services/
```

Now from another project:

```go
import "myapp/internal/services" // ❌ NOT allowed
```

👉 This is enforced by the Go compiler.

---

## 🔁 The clean way to think about it

Instead of mixing them, separate them:

### 1. Can this package be imported at all?

→ controlled by `internal/`

### 2. What inside the package is accessible?

→ controlled by uppercase/lowercase

---

## 🧩 The mental model that finally worked for me

* `internal/` → **who can enter the house**
* uppercase → **what is visible to guests**
* lowercase → **what stays private inside rooms**

---

## 🟢 Final takeaway

* Lowercase functions
  → private **inside a package**

* `internal/`
  → private **to your project**

* Both solve different problems
  → and you usually use both together

---

## ✍️ If you’re learning Go right now

If you're at that stage where:

* modules make sense
* packages kinda make sense
* but structure feels fuzzy

This is the piece that clears it up.

At least it did for me.

---

And honestly, this was one of those “ohhh okay” moments that made Go feel a lot more intentional 🚀

