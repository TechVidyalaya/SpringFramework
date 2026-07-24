---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 1
title: Spring Core Architecture - The Foundation of the Spring Framework
difficulty: Beginner
estimated_time: 75 Minutes
prerequisites:
  - Module 1 - Introduction to Spring Framework
learning_type:
  - Theory
  - Architecture
  - Practical
tags:
  - spring
  - spring core
  - architecture
  - ioc
version: 1.0
---

# Chapter 1 - Spring Core Architecture

> **"If Spring Boot is the car, Spring Core is the engine."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain what Spring Core is.
- Understand the architecture of Spring Core.
- Describe the responsibilities of Spring Core.
- Explain how every Spring project depends on Spring Core.
- Understand the complete startup flow of a Spring application.
- Prepare for IoC and Dependency Injection.

---

# 📚 Prerequisites

Before starting this chapter, you should know:

- Core Java
- Classes & Objects
- Constructors
- Interfaces
- Module 1

---

# 🗺 Learning Roadmap

```
Module 2

Introduction

↓

Spring Core Architecture  ← You are here

↓

IoC

↓

ApplicationContext

↓

Beans

↓

Bean Lifecycle

↓

Dependency Injection

↓

Component Scanning

↓

Bean Scopes
```

---

# ❓ Why Learn Spring Core?

Many beginners jump directly to annotations like:

```java
@Service
@Autowired
@Component
```

without understanding **who processes these annotations** or **what happens internally**.

Spring Core answers those questions.

Everything else in Spring builds on top of Spring Core.

---

# 🏢 Industry Story

Imagine constructing a modern hospital.

Before doctors, nurses, equipment, and patients arrive, someone must build:

- The building
- Power supply
- Water system
- Security
- Communication

Only then can the hospital function.

Spring Core plays the same role.

It builds the infrastructure that every Spring application relies on.

---

# 📖 What is Spring Core?

Spring Core is the foundational module of the Spring Framework.

It is responsible for:

- Creating application objects
- Managing their lifecycle
- Resolving dependencies
- Connecting objects together
- Configuring the application

Without Spring Core, Spring MVC, Spring Security, Spring Data JPA, and Spring Boot cannot function.

---

# 🎨 Spring Architecture

```
                    Spring Boot

                         │

       Spring MVC   Spring Security

             │             │

       Spring Data JPA     │

──────────────────────────────────────────

              Spring Core

──────────────────────────────────────────

        Java Runtime (JVM)
```

Spring Core is the foundation of the entire ecosystem.

---

# 🏗 Responsibilities of Spring Core

Spring Core performs several important tasks.

## 1. Object Creation

Instead of writing:

```java
StudentService service =
        new StudentService();
```

Spring creates the object.

---

## 2. Dependency Management

Suppose StudentService requires StudentRepository.

Instead of manually creating both objects,

Spring automatically injects StudentRepository into StudentService.

---

## 3. Bean Lifecycle

Spring decides:

- When to create an object
- When to initialize it
- When to destroy it

The developer doesn't have to manage this manually.

---

## 4. Configuration Management

Spring reads configuration from:

- application.properties
- application.yml
- Environment Variables
- Java Configuration

and makes those values available to the application.

---

## 5. Bean Scope Management

Spring determines whether an object should be:

- Singleton
- Prototype
- Request
- Session

This improves memory usage and scalability.

---

# 🧠 Behind the Scenes

Many developers think Spring simply creates objects.

Internally, much more happens.

Application starts

↓

SpringApplication.run()

↓

ApplicationContext is created

↓

Configuration is loaded

↓

Classpath scanning begins

↓

Bean definitions are created

↓

Dependencies are resolved

↓

Singleton beans are instantiated

↓

Application becomes ready

This startup sequence is the backbone of every Spring application.

---

# 💻 Running Example

Throughout Module 2, we will build a **Student Management System**.

Architecture:

```
Client

↓

StudentController

↓

StudentService

↓

StudentRepository

↓

Database
```

Initially, these classes will be empty.

As we progress through the module, Spring Core will manage each layer.

---

# 💻 Without Spring

```java
StudentRepository repository =
        new StudentRepository();

StudentService service =
        new StudentService(repository);

StudentController controller =
        new StudentController(service);
```

The developer manually creates and wires every object.

---

# 💻 With Spring

```java
@RestController
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }
}
```

Although the code appears simple, Spring performs significant work behind the scenes to provide the `StudentService` instance.

---

# 🔬 Behind the Scenes

When Spring encounters the above class:

1. Scans the package.
2. Finds the controller.
3. Creates a bean definition.
4. Creates a StudentService bean.
5. Resolves its dependencies.
6. Injects the bean through the constructor.
7. Stores both beans in the ApplicationContext.

The developer never writes `new StudentService()`.

---

# 📦 Java Object vs Spring Bean

| Java Object | Spring Bean |
|-------------|-------------|
| Created using `new` | Created by Spring |
| Managed by developer | Managed by Spring |
| Lifecycle managed manually | Lifecycle managed by Spring |
| No container | Stored inside ApplicationContext |

One of the biggest mindset shifts in Spring is understanding that **every bean is a Java object, but not every Java object is a Spring bean**.

---

# 🚀 Advantages of Spring Core

- Loose Coupling
- Better Testing
- Centralized Object Management
- Reduced Boilerplate Code
- Easier Configuration
- Better Maintainability
- Improved Scalability

---

# ⚠ Common Mistakes

❌ Thinking Spring Boot creates beans.

Reality:

Spring Core creates and manages beans.

Spring Boot simply makes Spring easier to configure.

---

❌ Thinking every Java object becomes a Spring Bean.

Only objects managed by the Spring container become beans.

---

# 💡 Best Practices

✔ Prefer constructor injection.

✔ Keep beans stateless where possible.

✔ Follow layered architecture.

✔ Use interfaces for business services.

✔ Avoid unnecessary bean creation.

---

# 🏢 Industry Insight

In enterprise applications containing hundreds or thousands of classes, manually creating and connecting objects quickly becomes unmanageable.

Spring Core centralizes object management, allowing teams to focus on implementing business features rather than maintaining infrastructure code.

---

# 👨‍💻 Senior Developer Notes

When debugging Spring applications, many issues trace back to Spring Core:

- Missing beans
- Duplicate beans
- Circular dependencies
- Incorrect bean scopes
- Package scanning issues

A solid understanding of Spring Core often makes diagnosing these problems much easier.

---

# 🐞 Debugging Corner

## Error

```
NoSuchBeanDefinitionException
```

Possible causes:

- Missing `@Component`
- Wrong package structure
- Component scanning not configured
- Bean not registered

In later chapters, we'll intentionally reproduce and fix these errors to understand how Spring resolves beans.

---

# 🎤 Interview Corner

### Beginner

1. What is Spring Core?
2. Why is Spring Core called the foundation of Spring?
3. What responsibilities does Spring Core have?

### Intermediate

4. Explain the difference between a Java object and a Spring Bean.
5. Which Spring projects depend on Spring Core?
6. Why does Spring manage object creation?

### Advanced

7. Explain the startup sequence of a Spring application.
8. What happens before the first HTTP request reaches a Spring Boot application?
9. What role does ApplicationContext play?

---

# 🧪 Hands-on Lab

## Objective

Prepare the Student Management System.

### Tasks

Create a Spring Boot project using Spring Initializr.

Create the following packages:

```
controller
service
repository
entity
config
```

Create empty classes:

- StudentController
- StudentService
- StudentRepository
- Student

Do not implement any logic yet.

These classes will evolve throughout Module 2.

---

# 🎯 Mini Challenge

Answer the following:

1. Why is Spring Core called the heart of the Spring Framework?
2. Can Spring MVC work without Spring Core?
3. What is the difference between a Java object and a Spring Bean?
4. Why does Spring create objects instead of developers?
5. What are the five primary responsibilities of Spring Core?

---

# 📌 Chapter Summary

Spring Core is the foundation of the Spring Framework. It manages object creation, dependency resolution, configuration, bean lifecycle, and communication between application components.

Understanding Spring Core is essential because every major Spring project—including Spring Boot, Spring MVC, Spring Security, and Spring Data JPA—depends on it.

In the next chapter, we'll study the principle that makes all of this possible: **Inversion of Control (IoC)**.

---

# 📋 Cheat Sheet

| Concept | Description |
|---------|-------------|
| Spring Core | Foundation of Spring |
| Primary Role | Manage Beans |
| Creates | Spring Beans |
| Connects | Dependencies |
| Stores Beans | ApplicationContext |
| Used By | Boot, MVC, Security, Data JPA |

---

# 🧠 Quiz

1. What is Spring Core?
2. Why is it important?
3. What is a Spring Bean?
4. How is a Spring Bean different from a normal Java object?
5. Which Spring module manages object creation?
6. What are the responsibilities of Spring Core?
7. What is the role of ApplicationContext?
8. Why is Spring Core essential for enterprise applications?

---

# 📖 What's Next?

➡ **Chapter 2 – Inversion of Control (IoC): Giving Control to the Framework**

You'll learn the design principle that transformed enterprise Java development and made modern Spring applications possible.
