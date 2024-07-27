---
title: 'Tutorial golang tickers'
description: 'An overview of the Golang tickers basics'
pubDate: 'Jun 14 2024'
heroImage: '/blog-placeholder-4.jpg'
tags: ["golang", "tickers", "temporal"]
---

# Tutorial: go tickers from time package

Tickers in Golang are essential tools for executing tasks at regular intervals. They are especially useful for creating timed operations in applications, such as monitoring systems, scheduling tasks, or generating periodic reports. This tutorial will guide you through the basics of using tickers in Golang, complete with code samples to demonstrate their practical applications.

## Introduction to Tickers

A ticker in Golang is a mechanism provided by the `time` package that sends a signal at regular intervals. This signal can be used to perform repetitive tasks in a Go application.

### Key Concepts

- **Ticker**: An object that sends a time event on a channel at regular intervals.
- **Channel**: A conduit through which a ticker sends events.

Tickers are commonly used in applications that need to perform actions at consistent intervals, such as refreshing data, sending periodic updates, or monitoring system health.

## Creating a Ticker

To create a ticker, you can use the `time.NewTicker` function, which takes a `time.Duration` argument that specifies the interval between ticks.

### Example: Basic Ticker

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // Create a ticker that ticks every second
    ticker := time.NewTicker(1 * time.Second)
    defer ticker.Stop()

    // Use a channel to receive ticker events
    done := make(chan bool)

    go func() {
        time.Sleep(5 * time.Second)
        done <- true
    }()

    // Loop that reads from the ticker channel
    for {
        select {
        case <-done:
            fmt.Println("Ticker stopped!")
            return
        case t := <-ticker.C:
            fmt.Println("Tick at", t)
        }
    }
}
```

In this example, a ticker is created to tick every second. A separate goroutine is used to stop the ticker after 5 seconds. The main goroutine listens for ticks and prints the current time each time a tick occurs.

## Stopping a Ticker

It is crucial to stop a ticker to avoid resource leaks. Tickers can be stopped by calling the `Stop` method.

### Example: Stopping a Ticker

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ticker := time.NewTicker(1 * time.Second)

    go func() {
        time.Sleep(3 * time.Second)
        ticker.Stop()
        fmt.Println("Ticker stopped")
    }()

    for t := range ticker.C {
        fmt.Println("Tick at", t)
    }
}
```

Here, the ticker is stopped after 3 seconds, and the program prints a message indicating that the ticker has been stopped.

## Using Tickers in Concurrency

Tickers are often used in concurrent applications to perform periodic tasks in the background. They can be used within goroutines to execute functions at regular intervals.

### Example: Concurrent Ticker Usage

```go
package main

import (
    "fmt"
    "time"
)

func worker(id int, ticker *time.Ticker, done chan bool) {
    for {
        select {
        case <-done:
            fmt.Printf("Worker %d stopped\\n", id)
            return
        case t := <-ticker.C:
            fmt.Printf("Worker %d: Tick at %v\\n", id, t)
        }
    }
}

func main() {
    done := make(chan bool)
    ticker := time.NewTicker(500 * time.Millisecond)

    for i := 1; i <= 3; i++ {
        go worker(i, ticker, done)
    }

    time.Sleep(3 * time.Second)
    close(done)
    ticker.Stop()
    fmt.Println("Main: Ticker stopped")
}
```

In this example, multiple worker goroutines are created, each listening to the same ticker. They print messages at every tick until the ticker is stopped.

## Advanced Ticker Patterns

### Ticker with Custom Function

You can use tickers to execute custom functions at regular intervals.

```go
package main

import (
    "fmt"
    "time"
)

func customTask() {
    fmt.Println("Executing custom task at", time.Now())
}

func main() {
    ticker := time.NewTicker(2 * time.Second)
    defer ticker.Stop()

    for {
        select {
        case <-ticker.C:
            customTask()
        }
    }
}
```

Here, `customTask` is called every 2 seconds, demonstrating how tickers can be integrated with custom functions.

### Dynamic Ticker Interval

You can change the interval of a ticker dynamically by stopping the old ticker and creating a new one with the desired interval.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    interval := 1 * time.Second
    ticker := time.NewTicker(interval)
    defer ticker.Stop()

    for i := 0; i < 5; i++ {
        select {
        case t := <-ticker.C:
            fmt.Println("Tick at", t)
        }
    }

    // Change interval
    fmt.Println("Changing interval to 2 seconds")
    ticker.Stop()
    ticker = time.NewTicker(2 * time.Second)

    for i := 0; i < 5; i++ {
        select {
        case t := <-ticker.C:
            fmt.Println("Tick at", t)
        }
    }
}
```

This example demonstrates changing the ticker interval from 1 second to 2 seconds.

## Using Tickers with Go Signals

In real-world applications, it’s crucial to handle OS signals for graceful shutdowns. This ensures that your application can clean up resources properly before exiting. We can use Go's `os/signal` package in conjunction with tickers to achieve this.

### Example: Ticker with OS Signals

```go
package main

import (
    "fmt"
    "os"
    "os/signal"
    "syscall"
    "time"
)

func main() {
    ticker := time.NewTicker(1 * time.Second)
    quit := make(chan struct{})

    // Channel to receive OS signals
    sigs := make(chan os.Signal, 1)
    signal.Notify(sigs, syscall.SIGINT, syscall.SIGTERM)

    // Goroutine to handle OS signals
    go func() {
        sig := <-sigs
        fmt.Println("\\nReceived signal:", sig)
        ticker.Stop()
        close(quit)
        fmt.Println("Ticker stopped due to signal")
    }()

    // Main ticker loop
    for {
        select {
        case <-quit:
            fmt.Println("Exiting...")
            return
        case t := <-ticker.C:
            fmt.Println("Tick at", t)
        }
    }
}
```

In this example:

- We create a ticker that ticks every second.
- We set up a channel `sigs` to receive OS signals such as `SIGINT` (Ctrl+C) and `SIGTERM`.
- A goroutine listens for signals and stops the ticker when a signal is received.
- The main loop continues to process ticker events until a signal is received and the `quit` channel is closed.

## Best Practices

1. **Always Stop the Ticker**: Always call `Stop` to free resources associated with the ticker.
2. **Use Goroutines Wisely**: Ensure that ticker-related code does not block the main goroutine.
3. **Monitor Ticker Behavior**: Be aware of the ticker’s behavior in high-load scenarios and ensure it meets the application’s requirements.
4. **Handle Panic**: Add proper error handling to avoid panics in ticker functions.
5. **Use Signals for Graceful Shutdowns**: Integrate signal handling to ensure your application can stop tickers and clean up gracefully.

## Conclusion

Tickers in Golang provide an effective way to execute periodic tasks. Understanding how to create, use, and manage tickers is essential for building robust and efficient applications. By following best practices and exploring advanced patterns, you can leverage tickers to enhance the functionality of your Go programs.

Feel free to experiment with the code examples provided in this tutorial to deepen your understanding of tickers in Golang. Happy coding!

---

If you have any questions or suggestions, leave a comment below. Share this guide with others who might find it helpful!

---

This concludes the expanded tutorial with an example of using tickers and Go signals for graceful shutdowns. This example is crucial for developing robust applications that need to handle unexpected exits gracefully.
