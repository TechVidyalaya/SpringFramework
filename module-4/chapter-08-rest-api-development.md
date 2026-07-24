---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 8
title: REST API Development
difficulty: Intermediate
estimated_reading_time: 75 Minutes
estimated_coding_time: 60 Minutes
estimated_lab_time: 45 Minutes
version: 1.0
---

# Chapter 8
# REST API Development

> **"A REST API is a contract between the client and the server. The clearer the contract, the easier systems can communicate."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what REST is and why it is widely adopted.
- Learn the principles of RESTful API design.
- Understand resources, URIs, and HTTP methods.
- Build CRUD APIs using Spring Boot.
- Follow REST naming conventions and best practices.
- Design scalable and maintainable enterprise APIs.

---

# Introduction

Imagine you're building the backend for a Student Management System.

Different clients need to access your application:

- A web application
- An Android app
- An iOS app
- Another university's software
- A reporting system

Should you write separate backend logic for every client?

**No.**

Instead, all clients communicate with a common interface known as a **REST API**.

```
               Student Management API

          ┌───────────────────────────┐
          │                           │
Web App ─▶│                           │
Mobile ──▶│        REST API           │
Admin ───▶│                           │
Partner ─▶│                           │
          └───────────────────────────┘
                     │
                     ▼
              Spring Boot Application
```

A well-designed REST API allows different systems to communicate consistently, regardless of the programming language or platform.

---

# What is an API?

API stands for **Application Programming Interface**.

An API defines how two software systems communicate.

Example:

```
Mobile App

↓

HTTP Request

↓

REST API

↓

Database

↓

HTTP Response

↓

Mobile App
```

The client doesn't need to know how the application works internally.

It only needs to know:

- Which endpoint to call
- Which HTTP method to use
- What request data to send
- What response to expect

---

# What is REST?

REST stands for **Representational State Transfer**.

It is an architectural style introduced by **Roy Fielding** in his doctoral dissertation in 2000.

REST is not a protocol or a framework.

Instead, it is a set of architectural principles for designing web services.

---

# Why REST Became Popular

Before REST, enterprise systems commonly used SOAP-based web services.

Although powerful, SOAP required:

- XML payloads
- Complex specifications
- Larger message sizes
- More processing

REST simplified communication by using:

- HTTP
- JSON
- Standard URLs
- Lightweight messages

Today, REST is the dominant style for building web APIs.

---

# REST Principles

A RESTful API follows several important principles.

## 1. Client-Server Architecture

The client and server have separate responsibilities.

```
Client

↓

REST API

↓

Server
```

The client focuses on presentation.

The server focuses on business logic and data.

---

## 2. Stateless Communication

Each request should contain all the information required to process it.

The server does **not** remember previous requests.

Example:

```
GET /students/101
```

The server processes the request independently.

Statelessness improves scalability because servers do not maintain client session state.

---

## 3. Resource-Based Design

Everything is treated as a **resource**.

Examples:

```
Student

Course

Teacher

Enrollment
```

Each resource has a unique URI.

Examples:

```
/students

/courses

/teachers

/enrollments
```

---

## 4. Uniform Interface

Every client communicates with the server using standard HTTP methods.

```
GET

POST

PUT

PATCH

DELETE
```

This consistency makes APIs predictable.

---

# Understanding Resources

A resource represents a business entity.

Examples:

```
Student

↓

/students
```

```
Course

↓

/courses
```

```
Teacher

↓

/teachers
```

Resources are identified by URIs.

---

# URI Design

Good URI design improves readability.

Good examples:

```
/students

/students/101

/courses

/courses/15
```

Poor examples:

```
/getStudents

/createStudent

/deleteCourse
```

Use nouns rather than verbs.

---

# HTTP Methods in REST

| HTTP Method | Purpose |
|--------------|----------|
| GET | Retrieve resources |
| POST | Create a resource |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Delete a resource |

Each method has a specific semantic meaning.

---

# CRUD Operations

REST maps naturally to CRUD operations.

| CRUD | HTTP Method |
|-------|-------------|
| Create | POST |
| Read | GET |
| Update | PUT / PATCH |
| Delete | DELETE |

This makes REST APIs intuitive for developers.

---

# Designing Student APIs

| Operation | HTTP Method | URI |
|------------|-------------|-----|
| Get All Students | GET | `/students` |
| Get Student | GET | `/students/{id}` |
| Create Student | POST | `/students` |
| Update Student | PUT | `/students/{id}` |
| Delete Student | DELETE | `/students/{id}` |

Notice that the URI remains consistent.

The HTTP method determines the operation.

---

# Building the Student Controller

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping
    public List<Student> getStudents() {
        return studentService.findAll();
    }

    @GetMapping("/{id}")
    public Student getStudent(
            @PathVariable Long id) {

        return studentService.findById(id);
    }

    @PostMapping
    public Student createStudent(
            @RequestBody Student student) {

        return studentService.save(student);
    }

    @PutMapping("/{id}")
    public Student updateStudent(
            @PathVariable Long id,
            @RequestBody Student student) {

        return studentService.update(id, student);
    }

    @DeleteMapping("/{id}")
    public void deleteStudent(
            @PathVariable Long id) {

        studentService.delete(id);
    }

}
```

This controller provides a complete CRUD API for the Student resource.

---

# Sample API Requests

## Retrieve All Students

```http
GET /students
```

---

## Retrieve a Student

```http
GET /students/101
```

---

## Create a Student

```http
POST /students

Content-Type: application/json

{
    "name": "Rahul",
    "email": "rahul@example.com",
    "department": "CSE"
}
```

---

## Update a Student

```http
PUT /students/101

Content-Type: application/json

{
    "name": "Rahul Sharma",
    "email": "rahul@example.com",
    "department": "IT"
}
```

---

## Delete a Student

```http
DELETE /students/101
```

---

# Appropriate HTTP Status Codes

| Operation | Status Code |
|------------|-------------|
| GET Successful | 200 OK |
| POST Successful | 201 Created |
| PUT Successful | 200 OK |
| DELETE Successful | 204 No Content |
| Invalid Request | 400 Bad Request |
| Resource Not Found | 404 Not Found |

Returning meaningful status codes improves API usability.

---

# Idempotency

One important REST concept is **idempotency**.

An operation is idempotent if executing it multiple times produces the same result.

| Method | Idempotent |
|----------|------------|
| GET | Yes |
| PUT | Yes |
| DELETE | Yes |
| POST | No |
| PATCH | Usually No |

Example:

Deleting the same student multiple times leaves the system in the same final state.

---

# API Versioning

As applications evolve, APIs change.

Instead of breaking existing clients, version your APIs.

Example:

```
/api/v1/students

/api/v2/students
```

This allows multiple API versions to coexist.

---

# Request Lifecycle

```
Client

↓

HTTP Request

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

Repository

↓

Service

↓

Controller

↓

JSON Response

↓

Client
```

Every REST endpoint follows this flow.

---

# Common Mistakes

❌ Using verbs in URIs.

```
/createStudent
```

---

❌ Returning incorrect status codes.

---

❌ Ignoring HTTP methods.

---

❌ Designing inconsistent URLs.

---

❌ Returning stack traces in production.

---

# Best Practices

- Use nouns for resources.
- Keep URIs simple and consistent.
- Use the correct HTTP method.
- Return appropriate status codes.
- Make APIs stateless.
- Validate all incoming requests.
- Use meaningful error responses.
- Version public APIs.

---

# Industry Insight

Most enterprise systems expose hundreds of REST endpoints.

Examples include:

```
/users

/orders

/payments

/products

/invoices

/notifications
```

Although the business domains differ, the design principles remain the same.

Developers familiar with REST can quickly understand and integrate with any well-designed API.

---

# Interview Corner

## Basic

1. What does REST stand for?
2. What is a REST API?
3. Why are APIs stateless?

---

## Intermediate

4. What is the difference between PUT and PATCH?
5. Why should REST APIs use nouns instead of verbs?
6. Explain CRUD mapping in REST.

---

## Advanced

7. Explain idempotency with examples.
8. Why is API versioning important?
9. Design REST endpoints for a Library Management System.

---

# Hands-on Lab

## Objective

Build a complete CRUD REST API for the Student Management System.

### Tasks

1. Create the `StudentController`.
2. Implement:
   - GET `/students`
   - GET `/students/{id}`
   - POST `/students`
   - PUT `/students/{id}`
   - DELETE `/students/{id}`
3. Test every endpoint using Postman or IntelliJ HTTP Client.
4. Verify that appropriate HTTP status codes are returned.
5. Refactor any URLs that do not follow REST naming conventions.

---

# Cheat Sheet

- REST = Representational State Transfer.
- REST APIs expose resources through URIs.
- Use nouns in endpoint names.
- GET retrieves data.
- POST creates resources.
- PUT replaces resources.
- PATCH partially updates resources.
- DELETE removes resources.
- Keep APIs stateless.
- Return proper HTTP status codes.

---

# Summary

REST provides a standardized approach for designing web APIs that are simple, scalable, and interoperable. By treating business entities as resources, using meaningful URIs, following HTTP semantics, and keeping communication stateless, developers can build APIs that are easy to understand, integrate, and maintain. Spring MVC and Spring Boot make implementing these principles straightforward through annotation-based programming and automatic request handling.

---

# What's Next?

➡ **Chapter 9 – Bean Validation**

In the next chapter, you'll learn how to validate incoming request data using Jakarta Bean Validation annotations such as `@NotNull`, `@NotBlank`, `@Email`, and `@Valid`, ensuring that only valid data enters your application.
