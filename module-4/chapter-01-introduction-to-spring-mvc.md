---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 1
title: Introduction to Spring MVC
difficulty: Beginner
estimated_reading_time: 40 Minutes
estimated_coding_time: 25 Minutes
estimated_lab_time: 20 Minutes
version: 1.0
---

# Chapter 1
# Introduction to Spring MVC

> **"Spring MVC is the bridge between a user's request and your application's business logic."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what Spring MVC is.
- Learn why Spring MVC was introduced.
- Understand the role of MVC in web application development.
- Explore the major components of Spring MVC.
- Understand how Spring MVC processes an HTTP request.
- Prepare for the upcoming chapters on Controllers, DispatcherServlet, and REST APIs.

---

# Introduction

Imagine you're building a **Student Management System**.

Students should be able to:

- Register themselves
- View their profile
- Update their information
- Search for courses
- Enroll in classes

How does a browser request reach your Java code?

When a user opens:

```
http://localhost:8080/students
```

how does Spring Boot know which Java method should execute?

This is where **Spring MVC** comes in.

Spring MVC receives incoming HTTP requests, processes them, invokes the appropriate business logic, and returns a response to the client.

---

# What is Spring MVC?

Spring MVC (Model-View-Controller) is Spring Framework's web framework for building web applications and REST APIs.

It helps developers:

- Handle HTTP requests
- Process business logic
- Return HTML pages or JSON responses
- Organize application code using the MVC design pattern

Spring MVC is built on top of the **Servlet API** and integrates seamlessly with Spring Boot.

---

# Why Spring MVC?

Without a web framework, developers would need to:

- Parse HTTP requests manually
- Handle URL routing
- Generate responses
- Manage controllers
- Process form data
- Handle validation

Spring MVC provides these features out of the box, allowing developers to focus on business logic instead of low-level web programming.

---

# Real-World Example

Consider an online banking application.

A customer clicks:

```
Transfer Money
```

The application needs to:

1. Receive the HTTP request.
2. Identify the correct controller.
3. Validate the request.
4. Execute business logic.
5. Save data to the database.
6. Return a success response.

Spring MVC manages the web layer, making this process structured and maintainable.

---

# What Does Spring MVC Do?

Spring MVC is responsible for:

- Receiving HTTP requests
- Mapping URLs to controller methods
- Reading request data
- Calling business services
- Returning responses
- Managing exceptions
- Validating user input

It acts as the communication layer between clients and your application.

---

# Understanding MVC

MVC stands for:

```
Model

↓

View

↓

Controller
```

Each component has a specific responsibility.

---

## Model

The **Model** represents the application's data and business objects.

Example:

```
Student

Course

Teacher

Enrollment
```

The model contains the information used by the application.

---

## View

The **View** is responsible for presenting data to the user.

Examples:

- HTML pages
- JSP pages
- Thymeleaf templates

In modern REST APIs, the view is often replaced by **JSON responses** consumed by frontend applications.

---

## Controller

The **Controller** receives HTTP requests and decides what should happen next.

Example:

```
GET /students

↓

StudentController

↓

StudentService

↓

Return Student List
```

Controllers coordinate the flow of data between the client and the application.

---

# Spring MVC Architecture

```
Browser

↓

Spring MVC

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Response

↓

Browser
```

Each layer has a clear responsibility, improving maintainability and scalability.

---

# Request Processing Overview

A simplified request flow looks like this:

```
Client Request

↓

DispatcherServlet

↓

Controller

↓

Business Logic

↓

Response

↓

Client
```

The **DispatcherServlet** is the central component of Spring MVC.

It receives every incoming request and forwards it to the appropriate controller.

We'll explore it in detail in the next chapters.

---

# Spring MVC vs Spring Boot

Many beginners confuse Spring MVC with Spring Boot.

| Spring MVC | Spring Boot |
|------------|-------------|
| Web framework | Application framework |
| Handles HTTP requests | Simplifies Spring application development |
| Provides MVC architecture | Provides auto-configuration and embedded servers |
| Part of Spring Framework | Built on top of Spring Framework |

Spring Boot uses Spring MVC to build web applications.

---

# Benefits of Spring MVC

- Clean separation of concerns
- Easy URL mapping
- REST API support
- Integration with Spring ecosystem
- Validation support
- Exception handling
- Testability
- Enterprise scalability

---

# Common Applications

Spring MVC is widely used for:

- REST APIs
- E-commerce websites
- Banking systems
- Hospital management systems
- Learning management systems
- Inventory management systems
- Enterprise business applications

---

# Common Mistakes

❌ Mixing business logic inside controllers.

❌ Accessing the database directly from controllers.

❌ Creating large, difficult-to-maintain controllers.

❌ Ignoring proper request validation.

---

# Best Practices

- Keep controllers lightweight.
- Place business logic inside service classes.
- Use meaningful URL mappings.
- Return proper HTTP status codes.
- Follow RESTful design principles.
- Separate concerns between Controller, Service, and Repository layers.

---

# Industry Insight

In enterprise applications, controllers typically perform only three tasks:

1. Receive the request.
2. Call the appropriate service.
3. Return the response.

Business logic, validation, and database access are delegated to dedicated layers, making applications easier to maintain and test.

---

# Interview Corner

## Basic

1. What is Spring MVC?
2. What does MVC stand for?
3. What is the role of a Controller?

---

## Intermediate

4. Explain the MVC architecture.
5. What is the difference between Spring MVC and Spring Boot?
6. Why should business logic not be placed inside controllers?

---

## Advanced

7. Explain the complete request flow in Spring MVC.
8. What are the advantages of separating Controller, Service, and Repository layers?
9. Why is Spring MVC suitable for enterprise applications?

---

# Hands-on Lab

## Objective

Create your first Spring MVC controller.

### Tasks

1. Create a new Spring Boot project.
2. Add the **Spring Web** starter.
3. Create a `StudentController`.
4. Create a GET endpoint:

```java
@GetMapping("/students")
public String getStudents() {
    return "Welcome to Student Management System";
}
```

5. Run the application.
6. Open:

```
http://localhost:8080/students
```

7. Verify the response in your browser.

---

# Cheat Sheet

- Spring MVC is Spring's web framework.
- MVC stands for Model, View, Controller.
- Controllers handle HTTP requests.
- Spring MVC simplifies web application development.
- Spring Boot uses Spring MVC to build REST APIs and web applications.

---

# Summary

Spring MVC is the foundation of web development in the Spring ecosystem. It provides a structured way to handle HTTP requests, process business logic, and generate responses while following the Model-View-Controller design pattern. By separating responsibilities across different layers, Spring MVC enables developers to build scalable, maintainable, and enterprise-ready web applications.

---

# What's Next?

➡ **Chapter 2 – MVC Architecture Explained**

In the next chapter, you'll take a deeper look at the **Model-View-Controller (MVC)** design pattern, understand the responsibility of each layer, and learn how they work together to process web requests efficiently.
