---
title: 'Golang TCP/IP Basics'
description: 'An overview of the Golang net package and tcp/ip basics.'
pubDate: 'Jun 15 2024'
heroImage: '/blog-placeholder-3.jpg'
tags: ['golang', 'tcp/ip', 'network']
---

# Building TCP Client and Server in Golang: Guide

In network programming, TCP (Transmission Control Protocol) is a fundamental protocol that provides reliable, ordered, and error-checked delivery of a stream of bytes between applications. In this blog post, we will explore how to create both a TCP client and server in Golang, from scratch. This tutorial is designed to take you step-by-step through the process, providing code samples and explanations for each part.


## Introduction to TCP in Golang

TCP is a protocol that allows two hosts to establish a connection and exchange streams of data. It ensures that data is delivered in order and without errors. Golang provides excellent support for TCP networking via its `net` package, making it straightforward to create both clients and servers.

### Key Concepts

- **TCP Server**: A program that listens for incoming TCP connections on a specific port.
- **TCP Client**: A program that establishes a connection to a TCP server to send and receive data.
- **Connection**: A two-way communication link established between the client and server.
- **Listener**: An object that waits for incoming connections on a specified network address.

## Creating a TCP Server

### Writing the Server Code

To create a TCP server in Golang, we will use the `net` package to listen for connections on a specific port and handle incoming data.

```go
package main

import (
    "bufio"
    "fmt"
    "net"
    "os"
)

func handleConnection(conn net.Conn) {
    defer conn.Close()
    fmt.Printf("Client connected: %s\\n", conn.RemoteAddr().String())
    scanner := bufio.NewScanner(conn)

    for scanner.Scan() {
        message := scanner.Text()
        fmt.Printf("Received: %s\\n", message)
        conn.Write([]byte("Echo: " + message + "\\n"))
    }

    if err := scanner.Err(); err != nil {
        fmt.Println("Error reading from connection:", err)
    }

    fmt.Printf("Client disconnected: %s\\n", conn.RemoteAddr().String())
}

func main() {
    listener, err := net.Listen("tcp", ":8080")
    if err != nil {
        fmt.Println("Error starting server:", err)
        os.Exit(1)
    }
    defer listener.Close()

    fmt.Println("TCP server listening on port 8080")
    for {
        conn, err := listener.Accept()
        if err != nil {
            fmt.Println("Error accepting connection:", err)
            continue
        }
        go handleConnection(conn)
    }
}
```

In this code:

- We create a TCP listener that listens on port 8080.
- `handleConnection` is a function that handles communication with a connected client.
- The server reads data from the client, echoes it back, and logs the interaction.

### Running the Server

Save the code to a file, say `server.go`, and run it using the following command:

```
go run server.go
```

You should see the server listening on port 8080, ready to accept connections.

## Creating a TCP Client

### Writing the Client Code

The TCP client will connect to the server, send data, and receive the response.

```go
package main

import (
    "bufio"
    "fmt"
    "net"
    "os"
)

func main() {
    conn, err := net.Dial("tcp", "localhost:8080")
    if err != nil {
        fmt.Println("Error connecting to server:", err)
        os.Exit(1)
    }
    defer conn.Close()

    fmt.Println("Connected to server. Type messages and press enter:")
    reader := bufio.NewReader(os.Stdin)

    for {
        fmt.Print("Message: ")
        message, _ := reader.ReadString('\\n')
        conn.Write([]byte(message))

        response, _ := bufio.NewReader(conn).ReadString('\\n')
        fmt.Println("Server response:", response)
    }
}
```

In this client code:

- We connect to the server running on `localhost` on port 8080.
- The client reads input from the user, sends it to the server, and prints the server's response.

### Running the Client

Save the client code to a file, say `client.go`, and run it using the following command:

```bash
go run client.go
```

You should see a prompt to enter messages, which will be sent to the server. The server’s response will be printed on the client console.

## Client-Server Interaction

Now that both the server and client are running, you can test the interaction:

1. Start the server.
2. Run the client.
3. Type messages into the client and observe them being echoed back by the server.

For example:

```
Server console:
TCP server listening on port 8080
Client connected: 127.0.0.1:52685
Received: Hello, server!
Client disconnected: 127.0.0.1:52685

Client console:
Connected to server. Type messages and press enter:
Message: Hello, server!
Server response: Echo: Hello, server!
```

## Handling Multiple Clients

To handle multiple clients concurrently, you can spawn a new goroutine for each connection.

```go
// Existing server code...

func main() {
    listener, err := net.Listen("tcp", ":8080")
    if err != nil {
        fmt.Println("Error starting server:", err)
        os.Exit(1)
    }
    defer listener.Close()

    fmt.Println("TCP server listening on port 8080")
    for {
        conn, err := listener.Accept()
        if err != nil {
            fmt.Println("Error accepting connection:", err)
            continue
        }
        go handleConnection(conn) // Handle each connection in a new goroutine
    }
}
```

With this setup, the server can handle multiple clients simultaneously without blocking on a single connection.

## Graceful Shutdown and Error Handling

It’s important to handle errors gracefully and ensure the server can shut down cleanly.

### Example: Handling Interrupt Signals

We can capture OS signals to handle a graceful shutdown:

```go
package main

import (
    "bufio"
    "fmt"
    "net"
    "os"
    "os/signal"
    "syscall"
)

func handleConnection(conn net.Conn) {
    defer conn.Close()
    fmt.Printf("Client connected: %s\\n", conn.RemoteAddr().String())
    scanner := bufio.NewScanner(conn)

    for scanner.Scan() {
        message := scanner.Text()
        fmt.Printf("Received: %s\\n", message)
        conn.Write([]byte("Echo: " + message + "\\n"))
    }

    if err := scanner.Err(); err != nil {
        fmt.Println("Error reading from connection:", err)
    }

    fmt.Printf("Client disconnected: %s\\n", conn.RemoteAddr().String())
}

func main() {
    listener, err := net.Listen("tcp", ":8080")
    if err != nil {
        fmt.Println("Error starting server:", err)
        os.Exit(1)
    }
    defer listener.Close()

    fmt.Println("TCP server listening on port 8080")

    // Channel to capture interrupt signals
    stop := make(chan os.Signal, 1)
    signal.Notify(stop, syscall.SIGINT, syscall.SIGTERM)

    go func() {
        for {
            conn, err := listener.Accept()
            if err != nil {
                fmt.Println("Error accepting connection:", err)
                continue
            }
            go handleConnection(conn)
        }
    }()

    <-stop
    fmt.Println("Shutting down server...")
}
```

In this example:

- We set up a channel to capture `SIGINT` and `SIGTERM` signals.
- When a signal is received, the server shuts down gracefully.

## Conclusion

Building a TCP client and server in Golang is straightforward, thanks to the powerful `net` package. This tutorial has covered:

- How to create a basic TCP server.
- How to create a TCP client.
- Handling client-server interactions.
- Managing multiple clients.
- Implementing graceful shutdowns and error handling.

With these concepts, you can build robust network applications that leverage TCP for reliable communication. Experiment with the provided code samples, and consider extending them to suit your specific needs.

Feel free to ask questions or share your own experiences with TCP networking in Golang in the comments below. Happy coding!
