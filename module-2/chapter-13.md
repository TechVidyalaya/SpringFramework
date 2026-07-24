---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 13
title: Bean Scopes - Understanding Object Lifetime in Spring
difficulty: Intermediate
estimated_time: 240 Minutes
version: 1.0
---

# Chapter 13
# Bean Scopes – Understanding Object Lifetime in Spring

> **"Spring doesn't just decide how to create a bean—it also decides how long that bean should live."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain what a bean scope is.
- Compare all standard Spring bean scopes.
- Understand singleton and prototype behavior.
- Understand web-specific scopes.
- Choose the appropriate scope for different scenarios.
- Avoid common concurrency mistakes.

---

# 📚 Prerequisites

- Spring Beans
- Bean Lifecycle
- Dependency Injection
- Component Scanning

---

# 🗺 Module Progress

```
Spring Beans

↓

Bean Lifecycle

↓

Dependency Injection

↓

Stereotypes

↓

📍 Bean Scopes

↓

Configuration
```

---

# ❓ What is Bean Scope?

Creating a bean is only part of the story.

Spring must also answer:

- How many instances should exist?
- Who receives those instances?
- When should they be destroyed?

These questions are answered by the bean's **scope**.

---

# 📖 Definition

A bean scope defines:

- the number of bean instances,
- how long they live,
- and who shares them.

Think of scope as the bean's **lifetime policy**.

---

# Spring Bean Scopes

Spring provides several scopes.

```
Bean Scopes

├── Singleton

├── Prototype

├── Request

├── Session

├── Application

└── WebSocket
```

The default scope is **Singleton**.

---

# 1. Singleton Scope

```java
@Service
@Scope("singleton")
public class StudentService {

}
```

Since singleton is the default, the annotation is usually omitted.

---

# Lifetime

```
Application Starts

↓

Bean Created

↓

Shared Everywhere

↓

Application Stops

↓

Bean Destroyed
```

Only **one instance** exists in the Spring container.

---

# Memory View

```
ApplicationContext

↓

StudentService

↓

0x123456
```

Every injection point receives the same object.

```
Controller A

↓

StudentService
      ↑

Controller B
      ↑

Controller C
```

---

# Why Singleton?

Most services are:

- Stateless
- Thread-safe
- Shared

Creating multiple copies would waste memory.

---

# Example

```
StudentController

↓

StudentService

↓

StudentRepository
```

All controllers share the same `StudentService` instance.

---

# Concurrency Note ⭐

Singleton does **not** mean "only one thread."

It means **one shared object**.

Multiple requests can execute methods on that object at the same time.

Good singleton beans avoid storing request-specific state in instance fields.

---

# 2. Prototype Scope

```java
@Component
@Scope("prototype")
public class ReportGenerator {

}
```

---

# Lifetime

```
Request Bean

↓

Create Object

↓

Return

↓

Forget
```

Every request to the container produces a new instance.

---

# Memory View

```
Request 1

↓

ReportGenerator

↓

0x111111

----------------

Request 2

↓

ReportGenerator

↓

0x222222
```

Each lookup gets a different object.

---

# Use Cases

Prototype is useful when the object:

- maintains temporary state,
- represents a short-lived task,
- should not be shared.

---

# Singleton vs Prototype

| Feature | Singleton | Prototype |
|----------|-----------|-----------|
| Instances | One | Many |
| Shared | Yes | No |
| Memory Usage | Lower | Higher |
| Default | Yes | No |

---

# Prototype Inside Singleton ⭐

Consider:

```java
@Service
public class StudentService {

    private final ReportGenerator reportGenerator;

}
```

If `ReportGenerator` is prototype scoped, what happens?

The prototype bean is injected **once** when the singleton is created.

Subsequent method calls continue using that same injected instance.

To obtain a fresh prototype instance for every operation, additional techniques such as `ObjectProvider`, `ObjectFactory`, or method injection are required.

---

# 3. Request Scope

Available in web applications.

```java
@RequestScope
public class RequestContext {

}
```

One bean instance per HTTP request.

---

# Timeline

```
HTTP Request

↓

Bean Created

↓

Controller

↓

Service

↓

Response

↓

Bean Destroyed
```

---

# Use Cases

- Request metadata
- Correlation IDs
- User request information

---

# 4. Session Scope

```java
@SessionScope
public class ShoppingCart {

}
```

One bean per user session.

---

# Timeline

```
User Login

↓

ShoppingCart Created

↓

Multiple Requests

↓

Same ShoppingCart

↓

Logout / Session Timeout

↓

Destroyed
```

---

# Typical Use Cases

- Shopping carts
- Multi-step forms
- Wizard state

---

# 5. Application Scope

```java
@ApplicationScope
public class ApplicationStatistics {

}
```

One instance shared across the entire web application.

Often used for application-wide caches or statistics.

---

# 6. WebSocket Scope

```java
@Scope("websocket")
```

One bean per WebSocket connection.

Useful for:

- Chat applications
- Live dashboards
- Real-time collaboration

---

# Scope Comparison

| Scope | Lifetime | Shared |
|--------|----------|--------|
| Singleton | Application | Yes |
| Prototype | Bean Lookup | No |
| Request | HTTP Request | No |
| Session | User Session | No |
| Application | Web Application | Yes |
| WebSocket | WebSocket Connection | No |

---

# How Spring Thinks

```
Need StudentService

↓

Check Scope

↓

Singleton?

↓

Already Exists?

↓

Yes

↓

Return Existing Instance
```

For a prototype bean:

```
Need ReportGenerator

↓

Prototype?

↓

Create New Instance

↓

Return

↓

Do Not Reuse
```

---

# Inside Spring 🧠

During bean lookup, Spring checks the bean definition.

```
Bean Definition

↓

Read Scope

↓

Singleton?

↓

Singleton Cache

↓

Return Existing

OR

↓

Prototype?

↓

Instantiate New Bean
```

The scope determines whether Spring retrieves an existing instance or creates a new one.

---

# Memory Diagram

```
ApplicationContext

├── StudentService

├── StudentRepository

├── UserService

└── OrderService

Singleton Cache
```

Prototype beans are generally **not** stored in the singleton cache after creation.

---

# Performance

Singleton:

✔ Lower memory usage

✔ Faster repeated lookups

✔ Preferred for stateless services

Prototype:

✔ Independent state

❌ Higher object creation cost

Choose a scope based on correctness rather than assumptions about performance.

---

# Common Mistakes

❌ Storing request-specific data in singleton beans.

This can lead to race conditions and incorrect behavior under concurrent requests.

---

❌ Using prototype scope everywhere.

Most service-layer components should remain singleton unless there is a clear need for multiple instances.

---

❌ Expecting a singleton to receive a new prototype instance automatically after startup.

---

# 🐞 Debugging Corner

### Problem

Two users see the same mutable data.

Cause:

A singleton bean stores user-specific state.

Solution:

Keep singleton beans stateless or move user-specific state to request/session-scoped components.

---

### Problem

Prototype bean behaves like a singleton.

Cause:

A prototype bean was injected into a singleton only once during startup.

---

# 🏢 Industry Insight

The vast majority of Spring service, repository, and controller beans in enterprise applications use the default singleton scope.

Request and session scopes are primarily reserved for web-specific state, while prototype scope is used selectively for stateful helper objects or task-oriented components.

---

# 🎤 Interview Corner

### Beginner

1. What is bean scope?
2. What is the default scope in Spring?
3. Difference between singleton and prototype?

---

### Intermediate

4. Explain request and session scopes.
5. Why are singleton beans usually stateless?
6. What happens when a prototype bean is injected into a singleton?

---

### Advanced

7. Explain how Spring manages singleton caching.
8. How does Spring resolve bean scopes internally?
9. When would you use `ObjectProvider` with prototype beans?

---

# 🧪 Hands-on Lab

## Objective

Observe bean scopes in action.

### Tasks

1. Create a `StudentService` bean (singleton).
2. Create a `ReportGenerator` bean (prototype).
3. Retrieve each bean twice from the `ApplicationContext`.
4. Compare object identities using `System.identityHashCode()`.
5. Observe:
   - Singleton returns the same instance.
   - Prototype returns different instances.
6. In a web project, create:
   - A `@RequestScope` bean.
   - A `@SessionScope` bean.
7. Send multiple HTTP requests and observe their lifecycle.

---

# 🎯 Architecture Decision

### Scenario 1

```
StudentService
```

Should it be:

- Singleton
- Prototype

Explain your choice.

---

### Scenario 2

```
ShoppingCart
```

Choose the most appropriate scope and justify your decision.

---

### Scenario 3

```
PdfReportGenerator
```

Should each report generation reuse one object or create a new one?

Discuss the trade-offs.

---

# 🎯 Mini Challenge

1. What is a bean scope?
2. Which scope is the default?
3. Why are singleton beans typically stateless?
4. When should prototype scope be used?
5. What happens if a prototype bean is injected into a singleton?

---

# 📌 Chapter Summary

Bean scopes determine how many instances of a bean exist, how long those instances live, and who shares them. Singleton is the default scope and is ideal for stateless services. Prototype creates a new instance for each lookup, while request, session, application, and WebSocket scopes address web-specific lifecycle requirements.

Understanding bean scopes helps developers design thread-safe applications, manage memory efficiently, and choose the correct lifecycle for each component.

---

# 📋 Cheat Sheet

| Scope | Instances | Lifetime | Common Use |
|--------|-----------|----------|------------|
| Singleton | One | Application | Services, repositories |
| Prototype | Many | Per lookup | Stateful helpers |
| Request | One | HTTP request | Request context |
| Session | One | User session | Shopping cart |
| Application | One | Web app | Shared cache |
| WebSocket | One | WebSocket session | Chat, live dashboards |

---

# 🧠 Quiz

1. What is the default bean scope?
2. Why are singleton beans shared?
3. When should prototype scope be used?
4. Why should singleton beans avoid mutable request-specific state?
5. What happens when a prototype bean is injected into a singleton?
6. Which scope is appropriate for a shopping cart?

---

# 📖 What's Next?

➡ **Chapter 14 – Java Configuration & `@Bean`: Creating Beans Without Component Scanning**

You'll learn when annotations like `@Component` aren't enough, how `@Configuration` and `@Bean` work together, how to register third-party library classes as Spring beans, and why Java-based configuration replaced XML in modern Spring applications.
