---
title: 'Flat structure for go web-app'
description: 'basics flat structure of go web application using std library'
pubDate: 'Jun 15 2024'
heroImage: '/blog-placeholder-4.jpg'
tags: ['golang', 'web-app', 'net/http']
---

# Golang WebApp basic flat structure

Simple and effective way to structure a Go application using `net/http`, `gorilla/mux` (a popular HTTP router), and an SQL library like `database/sql` with a preferred flat structure. I'll include a diagram to help visualize the structure.

### File Structure

Here's a recommended flat structure:

```
/myapp
    main.go
    handlers.go
    routes.go
    models.go
    db.go
    utils.go
```

### Explanation of Files

1. **main.go**: The entry point of the application. Initializes the application and starts the HTTP server.
2. **handlers.go**: Contains HTTP handler functions.
3. **routes.go**: Defines the routes and associates them with handler functions.
4. **models.go**: Defines the database models and methods to interact with the database.
5. **db.go**: Manages the database connection.
6. **utils.go**: Contains utility functions.

### Code Examples

### main.go

```go
package main

import (
    "log"
    "net/http"
    "myapp/db"
    "myapp/routes"
)

func main() {
    db.InitDB() // Initialize the database connection
    router := routes.NewRouter()

    log.Println("Starting server on :8080")
    log.Fatal(http.ListenAndServe(":8080", router))
}
```

### db.go

```go
package db

import (
    "database/sql"
    "log"
    _ "github.com/lib/pq" // PostgreSQL driver
)

var DB *sql.DB

func InitDB() {
    var err error
    DB, err = sql.Open("postgres", "user=username dbname=mydb sslmode=disable")
    if err != nil {
        log.Fatal(err)
    }

    if err = DB.Ping(); err != nil {
        log.Fatal(err)
    }

    log.Println("Database connection established")
}
```

### routes.go

```go
package routes

import (
    "github.com/gorilla/mux"
    "myapp/handlers"
)

func NewRouter() *mux.Router {
    router := mux.NewRouter()
    router.HandleFunc("/users", handlers.GetUsers).Methods("GET")
    router.HandleFunc("/users/{id}", handlers.GetUser).Methods("GET")
    router.HandleFunc("/users", handlers.CreateUser).Methods("POST")
    return router
}
```

### handlers.go

```go
package handlers

import (
    "encoding/json"
    "net/http"
    "myapp/models"
    "github.com/gorilla/mux"
    "strconv"
)

func GetUsers(w http.ResponseWriter, r *http.Request) {
    users, err := models.GetAllUsers()
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    json.NewEncoder(w).Encode(users)
}

func GetUser(w http.ResponseWriter, r *http.Request) {
    vars := mux.Vars(r)
    id, err := strconv.Atoi(vars["id"])
    if err != nil {
        http.Error(w, "Invalid user ID", http.StatusBadRequest)
        return
    }
    user, err := models.GetUserByID(id)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    json.NewEncoder(w).Encode(user)
}

func CreateUser(w http.ResponseWriter, r *http.Request) {
    var user models.User
    if err := json.NewDecoder(r.Body).Decode(&user); err != nil {
        http.Error(w, "Invalid request payload", http.StatusBadRequest)
        return
    }
    if err := models.CreateUser(&user); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    w.WriteHeader(http.StatusCreated)
    json.NewEncoder(w).Encode(user)
}
```

### models.go

```go
package models

import (
    "database/sql"
    "myapp/db"
)

type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email"`
}

func GetAllUsers() ([]User, error) {
    rows, err := db.DB.Query("SELECT id, name, email FROM users")
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    var users []User
    for rows.Next() {
        var user User
        if err := rows.Scan(&user.ID, &user.Name, &user.Email); err != nil {
            return nil, err
        }
        users = append(users, user)
    }
    return users, nil
}

func GetUserByID(id int) (*User, error) {
    row := db.DB.QueryRow("SELECT id, name, email FROM users WHERE id = $1", id)
    var user User
    if err := row.Scan(&user.ID, &user.Name, &user.Email); err != nil {
        if err == sql.ErrNoRows {
            return nil, nil
        }
        return nil, err
    }
    return &user, nil
}

func CreateUser(user *User) error {
    err := db.DB.QueryRow(
        "INSERT INTO users(name, email) VALUES($1, $2) RETURNING id",
        user.Name, user.Email).Scan(&user.ID)
    return err
}
```

### utils.go

```go
package utils

import (
    "log"
    "net/http"
    "time"
)

func LoggingMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        start := time.Now()
        next.ServeHTTP(w, r)
        log.Printf("%s %s %s", r.Method, r.RequestURI, time.Since(start))
    })
}
```

### Diagram

Here's a simple diagram showing how these components interact:

```
+------------------+
|      main.go     |
|------------------|
| - Initializes DB |
| - Starts Server  |
+------------------+
        |
        v
+------------------+
|    routes.go     |
|------------------|
| - Defines Routes |
| - Uses Handlers  |
+------------------+
        |
        v
+------------------+           +------------------+
|   handlers.go    |---------->|    models.go     |
|------------------|           |------------------|
| - HTTP Handlers  |           | - DB Operations  |
+------------------+           +------------------+
        |
        v
+------------------+
|     db.go        |
|------------------|
| - DB Connection  |
| - Initialization |
+------------------+
        |
        v
+------------------+
|    utils.go      |
|------------------|
| - Utility Funcs  |
| - Middleware     |
+------------------+
```

### Summary

This structure keeps your Go application simple and flat, making it easy to navigate and understand. Each file has a clear responsibility, helping you maintain and scale your application efficiently.
