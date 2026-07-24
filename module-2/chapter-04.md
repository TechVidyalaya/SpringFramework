---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 4
title: BeanFactory - The Basic IoC Container
difficulty: Intermediate
estimated_time: 90 Minutes
version: 1.0
---

# Chapter 4
# BeanFactory – The Basic IoC Container

> **"BeanFactory is the simplest implementation of the Spring IoC Container. It creates beans only when they are requested."**

---

# 🎯 Learning Objectives

After this chapter you will be able to:

- Explain what BeanFactory is.
- Understand why BeanFactory was introduced.
- Explain lazy initialization.
- Understand how BeanFactory creates beans.
- Compare BeanFactory with ApplicationContext.
- Explain BeanFactory in interviews.

---

# 📚 Prerequisites

- Spring Core Architecture
- IoC
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

📍 BeanFactory

↓

ApplicationContext

↓

Beans

↓

Dependency Injection
```

---

# ❓ Why Does Spring Need BeanFactory?

Suppose you have an application containing

```
900 Beans
```

Do all 900 need to be created immediately?

Probably not.

Some beans may never be used.

Creating every bean during startup wastes

- CPU
- Memory
- Startup time

Spring needed a mechanism that creates objects **only when required**.

That mechanism is BeanFactory.

---

# 📖 What is BeanFactory?

BeanFactory is the most basic implementation of Spring's IoC Container.

It is responsible for

- registering bean definitions,
- creating beans,
- injecting dependencies,
- managing bean lifecycle.

Unlike ApplicationContext,

it creates beans lazily.

---

# What Does "Factory" Mean?

In software engineering,

a Factory is an object whose responsibility is creating other objects.

Instead of writing

```java
new StudentService()
```

you ask the factory

```java
StudentService service =
beanFactory.getBean(StudentService.class);
```

The factory decides

- whether the object already exists,
- whether a new one should be created,
- how dependencies are injected.

---

# Traditional Java

```
Developer

↓

new StudentService()
```

---

# BeanFactory

```
Developer

↓

BeanFactory

↓

Creates StudentService

↓

Returns StudentService
```

---

# Real-Life Analogy

Imagine a library.

Without BeanFactory

You buy every book before reading it.

Huge cost.

Huge storage.

With BeanFactory

You ask the librarian.

The librarian gives you the book only when needed.

BeanFactory behaves similarly.

---

# Lazy Initialization

This is BeanFactory's defining characteristic.

Application starts.

```
Bean A

Bean B

Bean C

Bean D
```

BeanFactory creates

```
Nothing.
```

Only after

```java
getBean()
```

does Spring create the object.

---

# Startup Timeline

```
Application Starts

↓

Configuration Loaded

↓

Bean Definitions Registered

↓

Waiting...

↓

getBean(StudentService)

↓

Bean Created

↓

Dependency Injected

↓

Returned
```

---

# Internal Flow

Developer

↓

getBean()

↓

BeanFactory

↓

Check Cache

↓

Bean Exists?

↓

Yes → Return Existing Bean

↓

No

↓

Create Bean

↓

Inject Dependencies

↓

Store Bean

↓

Return Bean

---

# Memory Representation

Before getBean()

```
ApplicationContext

StudentService Definition

StudentRepository Definition

StudentController Definition
```

Only metadata exists.

---

After getBean()

```
ApplicationContext

StudentService Bean

StudentRepository Bean

StudentController Definition
```

Notice

only requested beans are instantiated.

---

# Example

```java
BeanFactory factory =
new XmlBeanFactory(resource);

StudentService service =
factory.getBean(StudentService.class);
```

At this moment

StudentService is created.

---

# Behind the Scenes

When getBean() executes

Spring performs

```
Find BeanDefinition

↓

Resolve Constructor

↓

Create Object

↓

Resolve Dependencies

↓

Inject Dependencies

↓

Run Initialization Callbacks

↓

Store Singleton

↓

Return Object
```

---

# Advantages

- Lower memory usage
- Faster initial startup
- Creates beans only when required
- Good for resource-constrained environments

---

# Disadvantages

Every first request requires bean creation.

If

```
PaymentService
```

takes

```
2 seconds
```

The first request waits.

Subsequent requests are fast.

---

# Industry Insight

Modern Spring Boot applications rarely use BeanFactory directly.

Instead,

they use ApplicationContext,

which eagerly creates singleton beans during startup.

However,

understanding BeanFactory helps explain

how Spring performs object creation internally.

---

# Common Mistakes

❌ Thinking BeanFactory creates every bean during startup.

Reality

It usually waits until getBean().

---

❌ Thinking BeanFactory is obsolete.

Reality

ApplicationContext is built on top of BeanFactory concepts.

Understanding BeanFactory helps understand Spring internals.

---

# Senior Developer Notes

Internally,

ApplicationContext delegates much of the bean creation work to classes implementing BeanFactory functionality.

Although you rarely interact with BeanFactory in everyday Spring Boot development,

the framework still relies on these underlying mechanisms.

---

# BeanFactory vs Manual new

| new | BeanFactory |
|------|-------------|
| Manual | Automatic |
| No DI | DI |
| No lifecycle | Lifecycle |
| No scope | Scope |
| No caching | Singleton cache |

---

# Debugging Corner

Problem

```
Bean creation is slow.
```

Possible causes

- Heavy constructor
- Database connection
- Network call
- Large configuration loading

Lazy initialization can delay this cost until the bean is actually needed.

---

# Interview Corner

### Beginner

1. What is BeanFactory?
2. Why is it called Factory?
3. What is lazy initialization?

---

### Intermediate

4. How does BeanFactory create beans?
5. Explain getBean().
6. Advantages of lazy initialization?

---

### Advanced

7. Explain BeanFactory internal flow.
8. What happens after getBean()?
9. Why is ApplicationContext preferred?

---

# Hands-on Lab

1. Create a simple Spring project.
2. Register two beans.
3. Enable debug logging.
4. Retrieve one bean using getBean().
5. Observe when the bean is instantiated.

---

# Mini Challenge

1. Why is BeanFactory lazy?
2. What is getBean()?
3. Why is it called a Factory?
4. Does BeanFactory manage bean lifecycle?
5. Name three advantages of lazy initialization.

---

# 📌 Chapter Summary

BeanFactory is the foundational implementation of Spring's IoC Container. It registers bean definitions and creates beans lazily, only when requested through `getBean()`. While modern applications usually work with `ApplicationContext`, understanding BeanFactory provides valuable insight into how Spring creates and manages objects behind the scenes.

---

# Cheat Sheet

| Concept | Description |
|---------|-------------|
| BeanFactory | Basic IoC Container |
| Creates Beans | On Demand |
| Initialization | Lazy |
| Main Method | getBean() |
| Advantage | Lower startup cost |

---

# 📖 What's Next?

➡ **Chapter 5 – ApplicationContext: The Enterprise IoC Container**

You'll learn why almost every Spring Boot application uses `ApplicationContext`, how it extends `BeanFactory`, and why eager bean initialization is often the better choice for production systems.
