---
layout: default
title: "Introduction to singleton design pattern"
date: 2025-08-11
excerpt: "An introduction to singleton design pattern."
---

# Thread-Safe Singleton Pattern in C#

## Introduction

In the world of software design, the Singleton pattern is a common design choice to ensure that a class has only one instance throughout the application’s lifecycle.

But what happens when you need that instance to be thread-safe in a multi-threaded environment? This article will walk you through the nuances of the Singleton pattern and thread safety in C#.

The article is structured in a question-and-answer format, as I believe this approach helps readers absorb and retain information more effectively. By addressing key questions and providing clear answers, you’ll gain a deeper understanding of the Singleton pattern and its application in real-world scenarios.

Throughout this article, we’ll answer common questions such as:

- Where is the Singleton pattern used?
- What are race conditions, and how can we prevent them?
- How do we implement thread-safe Singleton patterns?
- What are the best practices for working with Dependency Injection in the context of Singleton objects?

By the end of this article, you’ll have a solid understanding of how to create thread-safe Singleton instances in your applications, optimize your code, and avoid common pitfalls.

So, let’s dive in!

---

## 1. Where is the Singleton Pattern Used?

### Singleton Scenarios:

- **Logging Service:** A centralized logging system accessible application-wide.
- **Cache:** To store frequently accessed data and minimize re-computation.
- **Configuration Manager:** Loads configuration once and provides access globally.

---

## 2. What are Race Conditions and How Do They Relate to Locking for Thread Safety?

### What are Race Conditions?

Race conditions occur within a concurrent environment when proper synchronization is not in place. Concurrency involves managing multiple threads or processes running in parallel and ensuring they don’t interfere with each other in harmful ways.

In short:

- Multiple threads/processes access shared data simultaneously.
- The result depends on the execution order, potentially causing inconsistent or incorrect outcomes.

### What is Locking?

Locking prevents race conditions from occurring. It is a synchronization mechanism that ensures only one thread accesses critical sections at a time, preventing simultaneous modifications.

Think of it as a queue at a supermarket, where only one customer is processed at a time.

Locking helps prevent race conditions by ensuring that only one thread can execute the code inside a critical section at a time. When a thread acquires a lock on a resource, other threads attempting to access the same resource are blocked until the lock is released.

### What is the Root Cause of Race Condition?

The simultaneous modifications to shared data is the root cause of race conditions.

---

## 3. What are Critical Sections and Shared Resources?

A critical section is a part of a program where a shared resource (e.g., a variable, data structure, file, or hardware device) is accessed or modified. Since multiple threads may attempt to access the shared resource simultaneously, a critical section ensures that only one thread can access the resource at a time to prevent race conditions or inconsistent data.

### Example:

**Without Critical Section (Not thread safe)**

```csharp
public class CounterService
{
    //This is the shared resource. State.
    private int _count = 0; 
    public void Increment()
    {
        // Non-thread-safe. A shared resource is modified here.
        _count++; 
    }
    public int GetCount()
    {
        return _count;
    }
}
```

**With Critical Section (Thread Safety)**

```csharp
public class CounterService
{
    private int _count = 0;
    private readonly object _lock = new object();
    public void Increment()
    {
        lock (_lock)
        {
            // Critical section. Safely modifies the shared resource.
            _count++; 
        }
    }
    public int GetCount()
    {
        lock (_lock)
        {
            // Critical section. Safely reads the shared resource.
            return _count; 
        }
    }
}
```

## 4. What is Double-Checked Locking?
A technique to optimize thread-safe Singleton creation by reducing unnecessary locking overhead.

### How It Works:

- First Check: Check if the instance exists without locking. If it does, return it immediately.

- Second Check: If null, lock the critical section, check again, and then create the instance if it still doesn’t exist.

Example: Refer "How is traditional singleton implemented" for double-checked locking.


## 5. How is Traditional Singleton implemented?
It’s always good to understand the traditional way things were implemented. This gives us a sense of appreciation for how things have improved and also highlights the tools and frameworks available to make our lives easier.

The code below is self-intuitive, as we have already covered the concepts of locking, race conditions, critical sections and double-checked locking.

```csharp
public class CounterService
{
    private static CounterService _instance;
    private static readonly object _lock = new object();
    private int _count;

    // Private constructor to prevent instantiation
    private CounterService() 
    {
        // Initialize the counter
        _count = 0; 
    }

    // Public property to access the single instance
    public static CounterService Instance
    {
        get
        {
            if (_instance == null)
            {
                // Lock to ensure thread safety
                lock (_lock) 
                {
                    if (_instance == null)
                    {
                        _instance = new CounterService();
                    }
                }
            }
            return _instance;
        }
    }

    // Thread-safe method to increment the counter
    public void Increment()
    {
        // Lock to protect increment operation
        lock (_lock) 
        {
            _count++;
        }
    }

    // Thread-safe method to retrieve the counter value
    public int GetValue()
    {
        // Lock to protect read operation
        lock (_lock) 
        {
            return _count;
        }
    }
}
```

## 6. What is the other approach of creating Singleton objects?
In modern software development, Singletons using Dependency Injection (DI) frameworks are more common than the traditional approach. DI frameworks have become widely used due to their flexibility, ease of testing, and better support for managing object lifetimes.

That said, traditional Singleton is still used in scenarios where DI frameworks are not available or where there’s a need for a simple, lightweight solution without the overhead of a DI container. However, in most modern applications, especially in enterprise-level systems, Singletons implemented with DI frameworks are the standard approach.

## 7. What are the various object lifetimes which we use in a DI Framework?
There are 3 common object lifetimes:

Singleton: We already know.

Scoped: Request Based. The object is created once per request or session. The same instance is reused throughout the duration of a request or session but a new instance is created for each subsequent request or session.

Transient: Short Lived. The object is created each time it is requested. A new instance is provided every time the dependency is injected, regardless of whether it’s requested once or multiple times.

We won’t go into too much detail about all these object lifetimes here, as we will write a separate article explaining the differences in more detail.

## 8. Can Singleton Have Scoped or Transient Dependencies?
A common mistake when using the Singleton pattern is injecting scoped or transient dependencies into a singleton. Avoiding such mismatches requires a clear understanding of object lifetimes and their implications in dependency injection (DI) frameworks.

Transient in Singleton: Not recommended because the transient dependency behaves like a Singleton within the parent Singleton’s scope, which might be unintended.

Scoped in Singleton: Avoid direct injection as the Scoped dependency would share the Singleton’s lifetime, breaking its intended lifecycle.

Tip: Be cautious of lifetime mismatches in DI frameworks to prevent unexpected behaviours.


## 9. Can Singleton Prevent Race Conditions in DI frameworks?
No, not inherently. Singleton only ensures a single instance.

Yes, if implemented with thread safety (e.g., locks or double-checked locking). Please note that there is no need for double-check locking in case of DI frameworks.

How we can use singleton using DI and locking to prevent race conditions

```csharp
public interface ICounterService
{
    int IncrementCounter();
    int GetCounterValue();
}

public class CounterService : ICounterService
{
    private int _counter = 0;

    // Lock object to ensure thread-safety
    private readonly object _lock = new object(); 

    // Increment the counter in a thread-safe manner
    public int IncrementCounter()
    {
        // Ensure only one thread can increment the counter at a time
        lock (_lock)  
        {
            _counter++;
            return _counter;
        }
    }

    // Get the current counter value
    public int GetCounterValue()
    {
        return _counter;
    }
}
```

Registering the CounterService as singleton in DI framework:

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Register CounterService as a Singleton with DI.
        // DI will ensure only one instance of CounterService is used throughout the application
        services.AddSingleton<ICounterService, CounterService>();

        // Other service registrations...
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // Application setup (middleware, routing, etc.)
    }
}
```

## Why No Double-Checked Locking?
When you register a class as a singleton in a DI framework, the framework ensures that the instance is created only once, and it manages the threading concerns internally, making manual double-checked locking unnecessary.

The DI container guarantees thread-safety when resolving a singleton service, as long as the instance is not created manually outside of the container’s control.

So, by relying on DI for singleton creation, you can avoid the complexity of double-checked locking and leave the management of instance creation and lifecycle to the DI framework.

## Conclusion
Mastering the Singleton pattern and understanding the nuances of thread safety can greatly improve the performance and reliability of your applications. By using Dependency Injection, you can manage Singleton instances with ease, while the DI framework handles the lifecycle, though you may still need to implement locking to ensure thread safety when accessing shared resources.

Hope you enjoyed it as much as I enjoyed writing for you.