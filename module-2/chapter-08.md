---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 8
title: Dependency Injection (DI) - Wiring Objects the Spring Way
difficulty: Intermediate
estimated_time: 180 Minutes
version: 1.0
---

# Chapter 8
# Dependency Injection (DI)

> **"Don't ask for your dependencies. Let the framework provide them."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain Dependency Injection.
- Understand why DI exists.
- Explain the relationship between IoC and DI.
- Understand dependencies in object-oriented programming.
- Visualize dependency graphs.
- Explain DI confidently in interviews.

---

# 📚 Prerequisites

- Spring Core
- IoC
- IoC Container
- ApplicationContext
- Spring Beans
- Bean Lifecycle

---

# 🗺 Module Progress

```
Spring Core

↓

IoC

↓

Beans

↓

Bean Lifecycle

↓

📍 Dependency Injection

↓

Constructor Injection

↓

Setter Injection

↓

Field Injection
```

---

# ❓ What is a Dependency?

Suppose we have:

```java
public class StudentService {

    private StudentRepository repository;

}
```

Question:

Is StudentRepository independent?

No.

StudentService **depends** on StudentRepository.

That means

StudentRepository is a dependency.

---

# Real World Example

A Car depends on

- Engine
- Battery
- Tyres
- Fuel Tank

Without them,

the car cannot function.

Similarly,

StudentService depends on

```
StudentRepository

↓

Database
```

---

# Without Dependency Injection

```java
public class StudentService {

    private StudentRepository repository =
            new StudentRepository();

}
```

Looks simple.

But it creates several problems.

---

# Problems

Suppose tomorrow

```
MySQL

↓

PostgreSQL
```

changes.

Now StudentService is tightly coupled.

Testing becomes difficult.

Mocking becomes difficult.

Replacing implementations becomes difficult.

---

# Tight Coupling

```
StudentService

↓

new StudentRepository()
```

StudentService decides

which repository to use.

That violates

Dependency Inversion Principle.

---

# What is Dependency Injection?

Instead of creating dependencies,

they are provided from outside.

Old way

```
StudentService

↓

new Repository()
```

New way

```
Spring

↓

Repository

↓

StudentService
```

StudentService never creates the repository.

---

# Example

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository){

        this.repository = repository;

    }

}
```

Notice

No

```
new StudentRepository()
```

Spring injects it.

---

# Why is it called Injection?

Imagine a hospital.

A doctor doesn't manufacture medicines.

The pharmacy supplies them.

The medicine is

"injected"

into the doctor's workflow.

Similarly,

Spring injects dependencies into objects.

---

# IoC vs DI

This confuses many developers.

IoC

```
Design Principle
```

DI

```
Technique
```

Relationship

```
IoC

↓

implemented using

↓

Dependency Injection
```

Think of IoC as the goal and DI as one way to achieve it.

---

# Types of Dependency Injection

Spring supports three primary approaches.

```
Dependency Injection

↓

Constructor Injection

↓

Setter Injection

↓

Field Injection
```

We'll study each in separate chapters.

---

# Internal Working

Application Starts

↓

Component Scan

↓

StudentRepository Bean Created

↓

StudentService Bean Created

↓

Constructor Requires Repository

↓

Container Finds Repository Bean

↓

Injects Repository

↓

StudentService Ready

---

# Dependency Graph

```
StudentController

↓

StudentService

↓

StudentRepository

↓

DataSource

↓

Database
```

Spring builds this graph automatically.

---

# 🧠 How Spring Thinks

Spring examines constructors.

It asks:

```
StudentService

needs

StudentRepository
```

Container checks

```
Do I already have StudentRepository?

↓

Yes

↓

Inject it.
```

No developer code is required.

---

# Benefits of DI

- Loose Coupling
- Easier Testing
- Better Maintainability
- Reusable Components
- Improved Readability
- Better Scalability

---

# Real Production Example

Imagine replacing

```
MySQL Repository
```

with

```
MongoDB Repository
```

StudentService shouldn't change.

Only the injected implementation changes.

That's one of the biggest advantages of DI.

---

# Industry Insight

Large enterprise applications often contain thousands of dependencies.

Without DI,

changing one implementation could require changes across dozens of classes.

DI isolates these changes and reduces maintenance effort.

---

# Common Mistakes

❌ Creating dependencies manually.

```java
new StudentRepository()
```

This bypasses Spring.

---

❌ Thinking DI only works with Spring.

Dependency Injection is a design pattern and can be implemented without Spring.

Spring simply automates it.

---

# Debugging Corner

Problem

```
NullPointerException
```

Cause

Object created manually

```
new StudentService()
```

Spring never injected its dependencies.

---

# Interview Corner

## Beginner

1. What is Dependency Injection?
2. What is a dependency?
3. Why is DI useful?

---

## Intermediate

4. Difference between IoC and DI?
5. Explain DI using a real-world example.
6. What problems does DI solve?

---

## Advanced

7. Explain the internal flow of dependency injection.
8. Why does DI improve testing?
9. Can DI exist without Spring?

---

# Hands-on Lab

Create

StudentRepository

StudentService

StudentController

Use constructor injection.

Run the application.

Verify that

StudentService receives

StudentRepository automatically.

---

# Mini Challenge

1. Define Dependency Injection.
2. Why is it called Injection?
3. Difference between IoC and DI?
4. Name three types of DI.
5. Why is DI considered a best practice?

---

# 📌 Chapter Summary

Dependency Injection is a design pattern in which an object's dependencies are supplied by an external component rather than being created internally. In Spring, the IoC Container performs this responsibility automatically, resulting in loosely coupled, testable, and maintainable applications.

---

# Cheat Sheet

| Concept | Description |
|----------|-------------|
| Dependency | Required object |
| Dependency Injection | Providing dependencies from outside |
| IoC | Principle |
| DI | Implementation |
| Goal | Loose Coupling |

---

# Quiz

1. What is a dependency?
2. What is Dependency Injection?
3. Why is DI better than using `new`?
4. Difference between IoC and DI?
5. Name three types of DI.
6. Can DI exist without Spring?

---

# 📖 What's Next?

➡ **Chapter 9 – Constructor Injection: The Recommended Approach**

You'll learn why constructor injection is the preferred style in modern Spring applications, how Spring resolves constructor dependencies, why it improves immutability and testing, and how to handle multiple implementations using `@Primary` and `@Qualifier`.
