---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 7
title: Spring Bean Lifecycle - From Creation to Destruction
difficulty: Intermediate
estimated_time: 150 Minutes
version: 1.0
---

# Chapter 7
# Spring Bean Lifecycle – From Creation to Destruction

> **"A Spring Bean is born, configured, initialized, used, and finally destroyed. Understanding each stage is the key to mastering Spring."**

---

# 🎯 Learning Objectives

After this chapter, you will be able to:

- Explain every phase of the Spring Bean Lifecycle.
- Understand how Spring creates and initializes beans.
- Use `@PostConstruct` and `@PreDestroy`.
- Understand BeanPostProcessors.
- Explain bean destruction.
- Debug lifecycle-related issues.

---

# 📚 Prerequisites

- Spring Beans
- ApplicationContext
- IoC Container

---

# 🗺 Module Progress

```
Spring Core

↓

IoC

↓

IoC Container

↓

ApplicationContext

↓

Beans

↓

📍 Bean Lifecycle

↓

Dependency Injection

↓

Component Scanning
```

---

# ❓ Why Learn the Bean Lifecycle?

Imagine your application connects to:

- MySQL
- Redis
- Kafka
- RabbitMQ
- AWS S3

Questions arise:

- When should these connections be opened?
- When should caches be loaded?
- When should resources be released?
- When should cleanup occur?

The Bean Lifecycle provides answers.

---

# 📖 What is the Bean Lifecycle?

The Bean Lifecycle describes the complete journey of a Spring Bean:

1. Bean Definition
2. Bean Instantiation
3. Dependency Injection
4. Initialization
5. Ready for Use
6. Destruction

---

# 🧠 High-Level Lifecycle

```
@Component

↓

Component Scan

↓

Bean Definition

↓

Instantiate Bean

↓

Inject Dependencies

↓

@PostConstruct

↓

Bean Ready

↓

Application Running

↓

@PreDestroy

↓

Bean Destroyed
```

---

# 🏗 Phase 1 – Bean Definition

When Spring scans your application:

```java
@Service
public class StudentService {

}
```

it does **not** immediately create the object.

Instead, Spring creates metadata describing how the bean should be created.

This metadata is called a **Bean Definition**.

---

# Phase 2 – Bean Instantiation

Spring creates the Java object.

```
new StudentService()
```

Developers don't call `new`; Spring does.

---

# Phase 3 – Dependency Injection

Suppose the service depends on a repository.

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository){
        this.repository = repository;
    }

}
```

Spring first creates `StudentRepository`, then injects it into `StudentService`.

---

# Phase 4 – Aware Interfaces (Advanced)

Some beans need information about the container.

Spring offers *Aware* interfaces.

Examples:

- BeanNameAware
- BeanFactoryAware
- ApplicationContextAware
- EnvironmentAware

These let beans interact with the container when necessary.

---

# Phase 5 – BeanPostProcessor (Before Initialization)

Spring now allows custom processing before initialization.

```
Bean Created

↓

BeanPostProcessor

↓

@PostConstruct
```

Frameworks such as Spring Security and Spring AOP rely heavily on BeanPostProcessors.

---

# Phase 6 – Initialization

Now Spring executes initialization callbacks.

## Using @PostConstruct

```java
@Service
public class StudentService {

    @PostConstruct
    public void init() {

        System.out.println("Initializing StudentService");

    }

}
```

Typical uses:

- Load cache
- Validate configuration
- Open connections
- Initialize resources

---

# Phase 7 – Bean Ready

The bean is now fully initialized.

```
StudentController

↓

StudentService

↓

StudentRepository
```

The bean is available for the rest of the application.

---

# Phase 8 – Bean Destruction

When the application shuts down:

Spring invokes destruction callbacks.

Example:

```java
@PreDestroy
public void destroy(){

    System.out.println("Cleaning Resources");

}
```

Typical tasks:

- Close files
- Close database connections
- Stop schedulers
- Flush caches
- Release resources

---

# Complete Lifecycle Timeline

```
Application Starts

↓

Component Scan

↓

Bean Definition

↓

Instantiate Bean

↓

Dependency Injection

↓

BeanPostProcessor (Before)

↓

@PostConstruct

↓

BeanPostProcessor (After)

↓

Bean Ready

↓

Application Running

↓

Shutdown

↓

@PreDestroy

↓

Bean Destroyed
```

---

# 🏢 Running Example

StudentRepository

↓

StudentService

↓

StudentController

Application Startup

↓

Repository Created

↓

Service Created

↓

Controller Created

↓

@PostConstruct Executed

↓

Application Ready

Shutdown

↓

@PreDestroy Executed

---

# Internal Spring View

```
Bean Definition

↓

BeanFactory

↓

Constructor

↓

Populate Properties

↓

BeanPostProcessor Before

↓

@PostConstruct

↓

InitializingBean.afterPropertiesSet()

↓

Custom init-method

↓

BeanPostProcessor After

↓

Singleton Cache
```

Notice that `@PostConstruct` is only one part of the initialization process.

Spring performs several steps before and after it.

---

# Memory Representation

```
ApplicationContext

↓

studentService

Status

CREATED

↓

INITIALIZED

↓

READY

↓

DESTROYED
```

A bean transitions through these states during its lifecycle.

---

# Lifecycle Callback Options

| Mechanism | Purpose |
|-----------|---------|
| `@PostConstruct` | Initialization |
| `@PreDestroy` | Cleanup |
| `InitializingBean` | Initialization callback |
| `DisposableBean` | Destruction callback |
| `initMethod` | Custom init method |
| `destroyMethod` | Custom destroy method |

---

# Real-World Example

Suppose `StudentService` loads student data into memory.

```java
@PostConstruct
public void loadCache() {

    // Load frequently accessed student data

}
```

When the application shuts down:

```java
@PreDestroy
public void clearCache() {

    // Release resources

}
```

This ensures resources are prepared before use and cleaned up afterward.

---

# 🧠 How Spring Thinks

Spring doesn't simply create an object and forget about it.

It asks:

- Has the bean been instantiated?
- Are all dependencies available?
- Have initialization callbacks run?
- Is the bean ready for production use?
- Has cleanup been completed before shutdown?

The lifecycle ensures each bean reaches a valid state before it is used.

---

# 🏢 Industry Insight

Bean lifecycle callbacks are commonly used to:

- Warm application caches.
- Validate configuration.
- Start background schedulers.
- Open messaging connections.
- Register monitoring components.

Cleanup callbacks release these resources gracefully during shutdown.

---

# ⚠ Common Mistakes

❌ Performing expensive business logic inside constructors.

Constructors should primarily initialize object state.

Use `@PostConstruct` for initialization that depends on injected resources.

---

❌ Forgetting cleanup.

Leaving files, sockets, or network connections open can cause memory leaks or resource exhaustion.

---

# 🐞 Debugging Corner

## Problem

```text
NullPointerException inside constructor
```

Cause:

Attempting to use injected dependencies before Spring has completed dependency injection.

Move initialization logic to `@PostConstruct`.

---

# 🎤 Interview Corner

### Beginner

1. What is the Bean Lifecycle?
2. What is `@PostConstruct`?
3. What is `@PreDestroy`?

---

### Intermediate

4. Explain every phase of the lifecycle.
5. Why is dependency injection performed before initialization?
6. What is a BeanPostProcessor?

---

### Advanced

7. Explain the complete lifecycle from Bean Definition to Destruction.
8. When would you implement `InitializingBean`?
9. Why does Spring expose lifecycle callbacks?

---

# 🧪 Hands-on Lab

## Objective

Observe the Bean Lifecycle.

### Tasks

Create a service.

```java
@Service
public class StudentService {

    public StudentService() {
        System.out.println("Constructor");
    }

    @PostConstruct
    public void init() {
        System.out.println("PostConstruct");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("PreDestroy");
    }

}
```

Run the application.

Observe the console.

Then stop the application and observe the shutdown messages.

---

# 🎯 Mini Challenge

1. What is the purpose of `@PostConstruct`?
2. Why does dependency injection occur before initialization?
3. When is `@PreDestroy` executed?
4. Name three lifecycle callback mechanisms.
5. Why should constructors remain lightweight?

---

# 📌 Chapter Summary

The Spring Bean Lifecycle governs every stage of a managed bean—from metadata creation and instantiation to dependency injection, initialization, runtime usage, and destruction. Understanding this lifecycle helps developers write reliable initialization code, release resources correctly, and troubleshoot complex Spring applications.

---

# 📋 Cheat Sheet

| Stage | Description |
|--------|-------------|
| Bean Definition | Metadata registered |
| Instantiation | Object created |
| Dependency Injection | Dependencies resolved |
| Initialization | `@PostConstruct` and callbacks |
| Ready | Bean available for use |
| Destruction | `@PreDestroy` and cleanup |

---

# 🧠 Quiz

1. What is the Bean Lifecycle?
2. Why does Spring create a Bean Definition first?
3. When are dependencies injected?
4. What is the role of `@PostConstruct`?
5. What is a BeanPostProcessor?
6. When is `@PreDestroy` executed?
7. Why is understanding the lifecycle important in production?

---

# 📖 What's Next?

➡ **Chapter 8 – Dependency Injection (DI): Wiring Objects the Spring Way**

You'll learn how Spring automatically connects beans, why Dependency Injection is the preferred implementation of IoC, and how it reduces coupling while improving testability and maintainability.
