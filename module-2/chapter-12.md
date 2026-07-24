---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 12
title: Stereotype Annotations - Understanding @Component, @Service, @Repository, @Controller, @RestController & @Configuration
difficulty: Intermediate
estimated_time: 240 Minutes
version: 1.0
---

# Chapter 12
# Stereotype Annotations – Giving Meaning to Your Spring Components

> **"Every stereotype annotation creates a bean, but each one tells Spring and other developers a different story."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand stereotype annotations.
- Explain the purpose of each stereotype.
- Choose the correct annotation for each application layer.
- Understand additional behaviors provided by certain stereotypes.
- Explain how all stereotypes relate to `@Component`.

---

# 📚 Prerequisites

- Component Scanning
- Spring Beans
- Dependency Injection

---

# 🗺 Module Progress

```
Component Scanning

↓

📍 Stereotype Annotations

↓

Bean Scopes

↓

Configuration Classes
```

---

# ❓ Why Multiple Annotations?

Question:

If every stereotype creates a bean...

Why not use only:

```java
@Component
```

for everything?

Technically, you can.

But imagine a project with 800 classes.

```
@Component

@Component

@Component

@Component

@Component
```

Can you immediately tell which classes access the database?

Which contain business logic?

Which expose REST APIs?

No.

Stereotype annotations improve **architecture, readability, tooling, and framework behavior**.

---

# What is a Stereotype Annotation?

A stereotype annotation identifies the role a class plays within an application.

It also marks the class as a candidate for component scanning.

Examples:

```java
@Component

@Service

@Repository

@Controller

@RestController

@Configuration
```

---

# The Layered Architecture

```
Browser

↓

Controller

↓

Service

↓

Repository

↓

Database
```

Each stereotype naturally belongs to one layer.

---

# The Parent Annotation

Most stereotypes ultimately derive from `@Component`.

Conceptually:

```
@Component

├── @Service

├── @Repository

├── @Controller

└── @Configuration
```

`@RestController` is built on top of `@Controller` and `@ResponseBody`.

This means all of them are eligible for component scanning.

---

# 1. @Component

The most generic stereotype.

Example:

```java
@Component
public class FileUtility {

}
```

Use when the class does not clearly belong to a controller, service, or repository layer.

Typical examples:

- Utility classes
- Validators
- Helpers
- Formatters

---

# 2. @Service

```java
@Service
public class StudentService {

}
```

Represents the **business logic layer**.

Typical responsibilities:

- Validation
- Business rules
- Workflow orchestration
- Transactions (often in combination with `@Transactional`)

---

# Why @Service Exists

Even though `@Service` behaves like `@Component`, it communicates intent.

When another developer sees:

```java
@Service
```

they immediately understand:

"This class contains business logic."

---

# 3. @Repository

```java
@Repository
public class StudentRepository {

}
```

Represents the data access layer.

Responsibilities:

- Database operations
- Queries
- Persistence

---

# Hidden Feature ⭐

`@Repository` does more than create a bean.

It participates in **exception translation**.

Low-level persistence exceptions can be translated into Spring's `DataAccessException` hierarchy, giving applications a more consistent exception model across different persistence technologies.

This is one reason `@Repository` is preferred over a plain `@Component` for data access classes.

---

# 4. @Controller

```java
@Controller
public class StudentController {

}
```

Represents the web layer.

Typical responsibilities:

- Handle HTTP requests.
- Prepare models.
- Return view names.

Example:

```java
@GetMapping("/students")
public String students(Model model){

    return "students";

}
```

Primarily used in Spring MVC applications that render server-side views.

---

# 5. @RestController

Modern REST applications mostly use:

```java
@RestController
```

Example:

```java
@RestController
public class StudentController {

    @GetMapping("/students")
    public List<Student> getStudents(){

        return service.findAll();

    }

}
```

The returned object is automatically written to the HTTP response body (typically as JSON).

---

# Behind the Scenes

Conceptually:

```java
@RestController
```

acts like:

```java
@Controller

+

@ResponseBody
```

Every request handler automatically behaves as if `@ResponseBody` were present.

---

# 6. @Configuration

One of the most misunderstood annotations.

```java
@Configuration
public class AppConfig {

}
```

Purpose:

Create and configure beans explicitly.

Example:

```java
@Bean
public ObjectMapper objectMapper(){

    return new ObjectMapper();

}
```

This is especially useful for:

- Third-party libraries.
- Complex bean creation.
- External SDKs.

---

# @Configuration vs @Component

Both create beans.

However, `@Configuration` is intended for configuration classes and supports enhanced handling of `@Bean` methods so that singleton semantics are preserved even when those methods interact with each other.

This specialized behavior is one reason Spring recommends using `@Configuration` instead of a plain `@Component` for configuration classes.

---

# Choosing the Right Annotation

| Class Responsibility | Annotation |
|----------------------|------------|
| Generic helper | `@Component` |
| Business logic | `@Service` |
| Database access | `@Repository` |
| MVC Controller | `@Controller` |
| REST API | `@RestController` |
| Configuration | `@Configuration` |

---

# How Spring Thinks

During component scanning:

```
Found StudentService.class

↓

@Service?

↓

Yes

↓

Register BeanDefinition

↓

Role = Business Layer

↓

Continue Scanning
```

Spring registers the bean, while the stereotype communicates its intended architectural role.

---

# Inside Spring 🧠

Startup flow:

```
@ComponentScan

↓

Read Class Metadata

↓

Find Annotation

↓

@Service

↓

Meta-Annotation

↓

@Component

↓

Candidate Component

↓

BeanDefinition Created
```

Spring evaluates annotation metadata to determine whether a class is a candidate for registration.

---

# Architecture Diagram

```
REST Client

↓

@RestController

↓

@Service

↓

@Repository

↓

Database
```

Each layer has a clear responsibility.

---

# Performance

The choice between `@Component` and `@Service` has virtually no performance impact.

The primary benefit is:

- readability,
- maintainability,
- tooling support,
- architectural clarity.

---

# Common Mistakes

❌ Using `@Component` everywhere.

You lose the architectural meaning of your code.

---

❌ Annotating repositories with `@Service`.

You miss the intent of the data access layer and may not benefit from repository-specific behaviors.

---

❌ Returning JSON from `@Controller` without `@ResponseBody`.

In MVC applications, the framework interprets the returned `String` as a view name unless `@ResponseBody` (or `@RestController`) is used.

---

# 🐞 Debugging Corner

### Problem

```text
404 Not Found
```

Possible causes:

- Controller not discovered.
- Missing request mapping.
- Class outside scanned packages.

---

### Problem

Returned view instead of JSON.

Cause:

Using `@Controller` instead of `@RestController`, or forgetting `@ResponseBody`.

---

# 🏢 Industry Insight

Most enterprise applications adopt a layered architecture:

```
controller

↓

service

↓

repository
```

Using the appropriate stereotype annotations makes responsibilities clear, improves onboarding for new developers, and enables framework features where applicable.

---

# 🎤 Interview Corner

### Beginner

1. What is a stereotype annotation?
2. Why does Spring provide multiple stereotypes?
3. Difference between `@Controller` and `@RestController`?

---

### Intermediate

4. Why is `@Repository` preferred for data access?
5. When should `@Configuration` be used?
6. Is `@Service` different from `@Component`?

---

### Advanced

7. Explain meta-annotations.
8. How does Spring discover stereotype annotations?
9. Why is exception translation associated with `@Repository`?

---

# 🧪 Hands-on Lab

## Objective

Apply stereotype annotations correctly.

### Tasks

1. Create:

- StudentController
- StudentService
- StudentRepository
- FileUtility
- AppConfig

2. Annotate each class with the appropriate stereotype.

3. Add a `@Bean` method to `AppConfig`.

4. Print the bean count.

5. Verify every component is registered successfully.

---

# 🎯 Mini Challenge

1. Why not use `@Component` for every class?
2. Which stereotype belongs in the business layer?
3. What additional behavior is associated with `@Repository`?
4. How is `@RestController` conceptually related to `@Controller`?
5. When should `@Configuration` be used?

---

# 📌 Chapter Summary

Stereotype annotations identify the architectural role of Spring-managed classes while also making them eligible for component scanning. Although most stereotypes ultimately derive from `@Component`, each communicates a different responsibility and, in some cases, provides additional framework behavior. Choosing the correct stereotype leads to clearer architecture, easier maintenance, and better use of Spring's capabilities.

---

# 📋 Cheat Sheet

| Annotation | Typical Layer | Notes |
|------------|---------------|-------|
| `@Component` | Generic | General-purpose Spring bean |
| `@Service` | Business | Business logic |
| `@Repository` | Data Access | Persistence layer; exception translation |
| `@Controller` | MVC | Returns views |
| `@RestController` | REST | Returns response bodies |
| `@Configuration` | Configuration | Defines `@Bean` methods |

---

# 🧠 Quiz

1. What is a stereotype annotation?
2. Which stereotype is used for business logic?
3. Why use `@Repository` instead of `@Component`?
4. How is `@RestController` related to `@Controller`?
5. What is the purpose of `@Configuration`?
6. Why do all stereotype annotations ultimately derive from `@Component`?

---

# 📖 What's Next?

➡ **Chapter 13 – Bean Scopes: Understanding Singleton, Prototype, Request, Session, Application, and WebSocket Scopes**

You'll learn how long beans live, when Spring creates new instances, how scope affects memory and concurrency, and how choosing the right scope impacts application design and performance.
