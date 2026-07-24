---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 4
title: Controllers and Request Mapping
difficulty: Beginner
estimated_reading_time: 60 Minutes
estimated_coding_time: 45 Minutes
estimated_lab_time: 30 Minutes
version: 1.0
---

# Chapter 4
# Controllers and Request Mapping

> **"A URL is only an address. A Controller gives it meaning by deciding what should happen when someone visits it."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand the role of Controllers in Spring MVC.
- Learn the difference between `@Controller` and `@RestController`.
- Map URLs using `@RequestMapping`.
- Use specialized mapping annotations like `@GetMapping`, `@PostMapping`, `@PutMapping`, and `@DeleteMapping`.
- Organize REST endpoints using class-level and method-level mappings.
- Follow industry best practices for designing REST APIs.

---

# Introduction

In the previous chapter, we learned that every HTTP request first reaches the **DispatcherServlet**.

But how does Spring know which Java method should execute for a request like:

```
GET /students/101
```

Or:

```
POST /students
```

Or:

```
DELETE /students/101
```

The answer lies in **Controllers** and **Request Mapping**.

Controllers tell Spring:

> "When this URL is requested using this HTTP method, execute this Java method."

Without controllers, Spring MVC would have no idea how to process incoming requests.

---

# What is a Controller?

A **Controller** is a Spring component responsible for handling incoming HTTP requests.

Its responsibilities include:

- Receiving client requests
- Reading request data
- Calling the appropriate business service
- Returning the response

A controller should **not** contain business logic.

Instead, it acts as the bridge between the client and the service layer.

---

# Where Does a Controller Fit?

```
Browser

↓

DispatcherServlet

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
```

The Controller is the entry point into your application's business logic.

---

# Creating Your First Controller

Spring provides the `@RestController` annotation for building REST APIs.

```java
@RestController
public class StudentController {

}
```

This class is automatically detected during component scanning.

---

# What is @RestController?

`@RestController` is a specialized version of `@Controller`.

It tells Spring:

- This class handles HTTP requests.
- Return objects directly as JSON.
- Automatically serialize Java objects into HTTP responses.

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

}
```

---

# What is @Controller?

`@Controller` is primarily used for traditional web applications that return HTML views.

Example:

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "index";
    }

}
```

Instead of returning JSON, it returns the name of a view.

---

# @Controller vs @RestController

| Feature | `@Controller` | `@RestController` |
|----------|---------------|-------------------|
| Returns HTML Views | ✅ | ❌ |
| Returns JSON | With `@ResponseBody` | ✅ Automatically |
| Used for REST APIs | ❌ | ✅ |
| Used in Modern Spring Boot APIs | Rarely | Very Common |

For most modern backend applications, `@RestController` is the preferred choice.

---

# Understanding Request Mapping

A controller alone is not enough.

Spring also needs to know:

- Which URL?
- Which HTTP method?

This is achieved using **Request Mapping**.

---

# @RequestMapping

`@RequestMapping` maps HTTP requests to controller classes or methods.

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

}
```

Now every endpoint inside this controller starts with:

```
/students
```

---

# Class-Level Request Mapping

```
@RestController
@RequestMapping("/students")
public class StudentController {

}
```

Requests handled by this controller:

```
/students
/students/101
/students/search
```

Using class-level mapping avoids repeating the same URL prefix.

---

# Method-Level Request Mapping

Method-level mappings define specific endpoints.

```java
@GetMapping
public List<Student> getStudents() {

}
```

This handles:

```
GET /students
```

Another example:

```java
@GetMapping("/{id}")
public Student getStudent() {

}
```

This handles:

```
GET /students/101
```

---

# Specialized Mapping Annotations

Spring provides dedicated annotations for each HTTP method.

| Annotation | HTTP Method |
|------------|-------------|
| `@GetMapping` | GET |
| `@PostMapping` | POST |
| `@PutMapping` | PUT |
| `@DeleteMapping` | DELETE |
| `@PatchMapping` | PATCH |

These annotations improve readability and are preferred over using `@RequestMapping(method=...)`.

---

# GET Mapping

Used to retrieve data.

Example:

```java
@GetMapping
public List<Student> getStudents() {

    return studentService.findAll();

}
```

Request:

```
GET /students
```

---

# POST Mapping

Used to create new resources.

```java
@PostMapping
public Student createStudent() {

}
```

Request:

```
POST /students
```

---

# PUT Mapping

Used to update an existing resource.

```java
@PutMapping("/{id}")
public Student updateStudent() {

}
```

Request:

```
PUT /students/101
```

---

# DELETE Mapping

Used to remove a resource.

```java
@DeleteMapping("/{id}")
public void deleteStudent() {

}
```

Request:

```
DELETE /students/101
```

---

# PATCH Mapping

Used for partial updates.

Example:

```
PATCH /students/101
```

Instead of updating the entire object, only selected fields are modified.

---

# Complete REST Controller Example

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping
    public List<Student> getStudents() {
        return studentService.findAll();
    }

    @GetMapping("/{id}")
    public Student getStudent(@PathVariable Long id) {
        return studentService.findById(id);
    }

    @PostMapping
    public Student createStudent(@RequestBody Student student) {
        return studentService.save(student);
    }

    @PutMapping("/{id}")
    public Student updateStudent(
            @PathVariable Long id,
            @RequestBody Student student) {

        return studentService.update(id, student);
    }

    @DeleteMapping("/{id}")
    public void deleteStudent(@PathVariable Long id) {

        studentService.delete(id);

    }

}
```

This structure forms the foundation of most enterprise REST APIs.

---

# URL Design Best Practices

Good REST URLs:

```
/students

/students/101

/courses

/teachers

/enrollments
```

Avoid URLs like:

```
/getStudents

/createStudent

/deleteStudent

/updateStudent
```

HTTP methods already describe the action.

---

# REST Endpoint Design

| HTTP Method | URL | Operation |
|--------------|-----|-----------|
| GET | `/students` | Retrieve all students |
| GET | `/students/{id}` | Retrieve one student |
| POST | `/students` | Create student |
| PUT | `/students/{id}` | Replace student |
| PATCH | `/students/{id}` | Partially update |
| DELETE | `/students/{id}` | Delete student |

This follows RESTful design principles.

---

# Common Mistakes

❌ Writing business logic inside controllers.

❌ Creating one controller for the entire application.

❌ Using verbs in URLs.

❌ Returning database entities without validation.

❌ Ignoring proper HTTP methods.

---

# Best Practices

- Keep controllers lightweight.
- Group related endpoints together.
- Use nouns instead of verbs in URLs.
- Delegate business logic to services.
- Return meaningful HTTP status codes.
- Follow REST conventions consistently.

---

# Industry Insight

Enterprise applications often contain dozens or even hundreds of controllers.

Examples include:

- StudentController
- CourseController
- TeacherController
- UserController
- PaymentController
- OrderController

Each controller is responsible for a single business domain, making the codebase easier to understand and maintain.

---

# Interview Corner

## Basic

1. What is a Controller in Spring MVC?
2. What is `@RestController`?
3. What is `@RequestMapping`?

---

## Intermediate

4. Explain the difference between `@Controller` and `@RestController`.
5. Why are specialized mapping annotations preferred?
6. What is the purpose of class-level request mapping?

---

## Advanced

7. Design REST endpoints for a Library Management System.
8. Why should controllers remain lightweight?
9. What are RESTful URL design principles?

---

# Hands-on Lab

## Objective

Build a simple REST controller for the Student Management System.

### Tasks

1. Create a `StudentController`.
2. Add class-level mapping:

```java
@RequestMapping("/students")
```

3. Create endpoints:

- GET `/students`
- GET `/students/{id}`
- POST `/students`
- PUT `/students/{id}`
- DELETE `/students/{id}`

4. Test the endpoints using Postman or IntelliJ HTTP Client.

---

# Cheat Sheet

- `@Controller` returns HTML views.
- `@RestController` returns JSON responses.
- `@RequestMapping` maps URLs.
- `@GetMapping` handles GET requests.
- `@PostMapping` handles POST requests.
- `@PutMapping` handles PUT requests.
- `@DeleteMapping` handles DELETE requests.
- Use nouns instead of verbs in REST URLs.

---

# Summary

Controllers are the heart of the web layer in a Spring MVC application. They receive HTTP requests, delegate business logic to the service layer, and return responses to clients. Spring simplifies request handling through annotations such as `@RestController`, `@RequestMapping`, and the specialized HTTP mapping annotations. By following RESTful design principles and keeping controllers lightweight, developers can build clean, scalable, and maintainable web APIs.

---

# What's Next?

➡ **Chapter 5 – Request and Response Handling**

In the next chapter, you'll learn how Spring MVC processes incoming HTTP requests and generates responses. We'll explore `HttpServletRequest`, `HttpServletResponse`, request headers, response status codes, and how Spring automatically binds request data to Java objects.
