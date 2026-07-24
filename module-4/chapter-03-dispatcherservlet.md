---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 3
title: DispatcherServlet – The Front Controller of Spring MVC
difficulty: Intermediate
estimated_reading_time: 50 Minutes
estimated_coding_time: 35 Minutes
estimated_lab_time: 25 Minutes
version: 1.0
---

# Chapter 3
# DispatcherServlet – The Front Controller of Spring MVC

> **"Every HTTP request in a Spring MVC application begins its journey with the DispatcherServlet."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what the DispatcherServlet is.
- Learn why Spring MVC uses the Front Controller pattern.
- Understand the complete request processing lifecycle.
- Explore the major components that work with the DispatcherServlet.
- Follow the journey of an HTTP request from browser to controller and back.
- Prepare for building REST APIs using Spring MVC.

---

# Introduction

Suppose a user opens the following URL:

```
GET http://localhost:8080/students/101
```

Several questions immediately arise:

- Which controller should handle this request?
- Which method should be executed?
- How are request parameters extracted?
- How is the response converted into JSON?
- Who handles errors if something goes wrong?

Instead of every controller performing these tasks independently, Spring MVC delegates all incoming requests to a single component called the **DispatcherServlet**.

It acts as the central coordinator of the entire web framework.

---

# What is DispatcherServlet?

The **DispatcherServlet** is the **Front Controller** of Spring MVC.

It receives every incoming HTTP request and coordinates its processing.

Its responsibilities include:

- Receiving client requests
- Finding the correct controller
- Invoking controller methods
- Managing request and response objects
- Handling exceptions
- Returning the final response

Think of it as the **traffic controller** of a Spring MVC application.

---

# Front Controller Pattern

Instead of sending requests directly to different controllers,

every request first reaches one central controller.

```
Browser

↓

DispatcherServlet

↓

Controller

↓

Response

↓

Browser
```

This architectural pattern is called the **Front Controller Pattern**.

It provides a single entry point for all web requests.

---

# Why Use a Front Controller?

Imagine a shopping mall with multiple stores.

Instead of visitors directly entering restricted areas,

everyone first enters through the main entrance where:

- Security checks visitors
- Directions are provided
- Information is shared

Similarly,

DispatcherServlet performs common processing before forwarding requests to controllers.

This keeps controllers simple and focused.

---

# Request Processing Lifecycle

A simplified request flow is shown below.

```
Browser

↓

DispatcherServlet

↓

Handler Mapping

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Controller

↓

Http Message Converter

↓

JSON Response

↓

Browser
```

DispatcherServlet manages this complete workflow.

---

# Step 1: Client Sends Request

Example:

```
GET /students/101
```

The browser sends an HTTP request to the Spring Boot application.

---

# Step 2: DispatcherServlet Receives the Request

The embedded web server forwards the request to the DispatcherServlet.

```
Tomcat

↓

DispatcherServlet
```

From this point onward,

Spring MVC manages the request.

---

# Step 3: Find the Controller

DispatcherServlet asks:

> "Which controller can handle this request?"

It consults the **Handler Mapping** component.

Example:

```
/students/{id}

↓

StudentController
```

The correct controller is identified.

---

# Step 4: Execute Controller Method

DispatcherServlet invokes the matching controller method.

Example:

```java
@GetMapping("/{id}")
public Student getStudent(@PathVariable Long id) {

    return studentService.findById(id);

}
```

The controller performs only request handling and delegates business logic to the service layer.

---

# Step 5: Business Logic Executes

The controller calls:

```
StudentService

↓

StudentRepository

↓

Database
```

The requested data is retrieved.

---

# Step 6: Return the Result

The controller returns a Java object.

Example:

```java
Student
```

At this point,

the response is **not yet JSON**.

---

# Step 7: Convert Response

DispatcherServlet uses an **Http Message Converter**.

```
Student Object

↓

Jackson

↓

JSON
```

The Java object is automatically converted into JSON.

---

# Step 8: Send Response

Finally,

DispatcherServlet sends the HTTP response back to the browser.

Example:

```json
{
  "id": 101,
  "name": "Rahul",
  "email": "rahul@example.com"
}
```

The request processing is complete.

---

# Components Used by DispatcherServlet

| Component | Responsibility |
|-----------|----------------|
| Handler Mapping | Finds the appropriate controller |
| Controller | Handles the request |
| Service | Executes business logic |
| Repository | Accesses the database |
| Http Message Converter | Converts Java objects into JSON/XML |
| Exception Resolver | Handles exceptions |

These components work together behind the scenes.

---

# Complete Request Lifecycle

```
Browser

↓

Embedded Tomcat

↓

DispatcherServlet

↓

Handler Mapping

↓

StudentController

↓

StudentService

↓

StudentRepository

↓

Database

↓

StudentRepository

↓

StudentService

↓

StudentController

↓

Http Message Converter

↓

HTTP Response

↓

Browser
```

Understanding this lifecycle is essential for debugging Spring MVC applications.

---

# DispatcherServlet in Spring Boot

Spring Boot automatically configures the DispatcherServlet.

You do **not** need to create or register it manually.

This is one of the benefits of Spring Boot's Auto Configuration.

---

# Benefits of DispatcherServlet

- Centralized request handling
- Simplified controller development
- Automatic request mapping
- Automatic response conversion
- Integrated exception handling
- Easy framework extension

---

# Common Mistakes

❌ Assuming controllers receive requests directly.

❌ Writing business logic inside DispatcherServlet (it should remain framework-managed).

❌ Forgetting that every request passes through DispatcherServlet.

❌ Confusing DispatcherServlet with the embedded Tomcat server.

---

# Best Practices

- Keep controllers lightweight.
- Let DispatcherServlet handle request routing.
- Return domain objects instead of manually creating JSON.
- Use proper exception handling.
- Understand the request lifecycle for easier debugging.

---

# Industry Insight

Every enterprise Spring MVC application relies on DispatcherServlet.

Whether your application has:

- 10 endpoints
- 100 endpoints
- 10,000 endpoints

every request still passes through a single DispatcherServlet instance before reaching the appropriate controller.

Understanding this component makes debugging much easier when requests fail or are incorrectly mapped.

---

# Interview Corner

## Basic

1. What is DispatcherServlet?
2. What is the Front Controller Pattern?
3. Does every request pass through DispatcherServlet?

---

## Intermediate

4. Explain the request processing lifecycle.
5. What is Handler Mapping?
6. How does Spring convert Java objects into JSON?

---

## Advanced

7. How does DispatcherServlet interact with controllers?
8. Why is DispatcherServlet considered the heart of Spring MVC?
9. What happens if no controller matches an incoming request?

---

# Hands-on Lab

## Objective

Observe DispatcherServlet in action.

### Tasks

1. Create a Spring Boot Web application.
2. Add a `StudentController`.
3. Create a GET endpoint.
4. Run the application.
5. Send a request using Postman or a browser.
6. Enable DEBUG logging for Spring MVC and observe the request flow in the console.

---

# Cheat Sheet

- DispatcherServlet is the Front Controller of Spring MVC.
- Every HTTP request passes through DispatcherServlet.
- It finds the correct controller using Handler Mapping.
- It invokes controller methods.
- It converts Java objects into JSON using Http Message Converters.
- Spring Boot configures DispatcherServlet automatically.

---

# Summary

The DispatcherServlet is the core component of Spring MVC and the entry point for every HTTP request. It coordinates request routing, controller execution, response generation, and exception handling while hiding the complexity of the underlying web framework. Understanding the DispatcherServlet provides a strong foundation for mastering Spring MVC and building robust RESTful applications.

---

# What's Next?

➡ **Chapter 4 – Controllers and Request Mapping**

In the next chapter, you'll learn how to create controllers using `@Controller` and `@RestController`, map URLs using `@RequestMapping` and its variants, and build your first REST endpoints.
