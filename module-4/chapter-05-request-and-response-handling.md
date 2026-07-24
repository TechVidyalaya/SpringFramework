---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 5
title: Request and Response Handling
difficulty: Intermediate
estimated_reading_time: 60 Minutes
estimated_coding_time: 45 Minutes
estimated_lab_time: 30 Minutes
version: 1.0
---

# Chapter 5
# Request and Response Handling

> **"Every web application is built on one simple conversation: the client sends a request, and the server sends back a response."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand the HTTP Request-Response lifecycle.
- Learn how Spring MVC handles requests and generates responses.
- Work with `HttpServletRequest` and `HttpServletResponse`.
- Read HTTP headers, query parameters, and request metadata.
- Return different types of HTTP responses.
- Understand how Spring automatically converts Java objects into JSON.

---

# Introduction

Suppose a student opens the following URL:

```
GET http://localhost:8080/students/101
```

Before your controller executes, the browser sends a complete **HTTP Request**.

That request contains much more than just a URL.

It includes:

- HTTP Method
- URL
- Headers
- Cookies
- Query Parameters
- Request Body (for POST/PUT)
- Client Information

Similarly, the server doesn't simply return data.

It returns an **HTTP Response** containing:

- Status Code
- Headers
- Response Body
- Content Type

Understanding this communication is essential for building professional REST APIs.

---

# The HTTP Request-Response Cycle

Every interaction between a client and a server follows the same lifecycle.

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

Controller

↓

HTTP Response

↓

Client
```

Spring MVC abstracts much of this complexity, but understanding the underlying flow helps you build better applications.

---

# What is an HTTP Request?

An HTTP Request is a message sent by the client asking the server to perform an action.

Example:

```
GET /students/101 HTTP/1.1

Host: localhost:8080

Accept: application/json
```

A request typically contains:

- Request Line
- Headers
- Body (optional)

---

# Components of an HTTP Request

```
HTTP Method

↓

URL

↓

Headers

↓

Body (optional)
```

Example:

```
POST /students

Headers:
Content-Type: application/json

Body:
{
   "name":"Rahul",
   "email":"rahul@example.com"
}
```

---

# HTTP Methods

| Method | Purpose |
|----------|----------|
| GET | Retrieve data |
| POST | Create a resource |
| PUT | Replace a resource |
| PATCH | Partially update |
| DELETE | Delete a resource |

Each method communicates the client's intention to the server.

---

# What is an HTTP Response?

After processing the request, the server returns an HTTP Response.

Example:

```
HTTP/1.1 200 OK

Content-Type: application/json

{
   "id":101,
   "name":"Rahul"
}
```

A response consists of:

- Status Code
- Headers
- Response Body

---

# Understanding HTTP Status Codes

| Code | Meaning |
|--------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |

Choosing the correct status code improves API clarity.

---

# Spring MVC Request Handling

When a request reaches Spring MVC, the framework automatically creates request and response objects.

```
Browser

↓

DispatcherServlet

↓

Controller

↓

Response
```

Controllers can access these objects whenever needed.

---

# HttpServletRequest

`HttpServletRequest` represents the incoming client request.

Example:

```java
@GetMapping("/info")
public String requestInfo(HttpServletRequest request) {

    return request.getMethod();

}
```

This returns:

```
GET
```

---

# Useful Methods of HttpServletRequest

| Method | Description |
|----------|-------------|
| `getMethod()` | Returns HTTP method |
| `getRequestURI()` | Returns request path |
| `getHeader()` | Reads request headers |
| `getParameter()` | Reads query parameters |
| `getRemoteAddr()` | Client IP address |
| `getCookies()` | Returns cookies |

These methods provide access to request metadata.

---

# Reading Request Headers

Example:

```java
@GetMapping("/agent")
public String userAgent(HttpServletRequest request) {

    return request.getHeader("User-Agent");

}
```

Possible output:

```
Mozilla/5.0 ...
```

Headers often contain information such as:

- Browser
- Language
- Authentication Tokens
- Content Type

---

# HttpServletResponse

`HttpServletResponse` represents the server's response.

Example:

```java
@GetMapping("/status")
public void response(HttpServletResponse response) {

    response.setStatus(201);

}
```

This sends:

```
201 Created
```

---

# Returning Java Objects

Most Spring Boot applications return Java objects.

Example:

```java
@GetMapping("/{id}")
public Student getStudent() {

    return new Student(101L,
                       "Rahul",
                       "rahul@example.com");

}
```

Spring automatically converts the object into JSON.

Response:

```json
{
  "id":101,
  "name":"Rahul",
  "email":"rahul@example.com"
}
```

---

# Automatic JSON Conversion

How does this happen?

```
Student Object

↓

Jackson Library

↓

JSON

↓

HTTP Response
```

Spring Boot automatically configures the Jackson library for JSON serialization.

No manual conversion is required.

---

# Returning Collections

Controllers can also return collections.

Example:

```java
@GetMapping
public List<Student> getStudents() {

    return studentService.findAll();

}
```

Spring converts the list into a JSON array.

---

# Returning ResponseEntity

Sometimes you need full control over the HTTP response.

Spring provides the `ResponseEntity` class.

Example:

```java
@GetMapping("/{id}")
public ResponseEntity<Student> getStudent(
        @PathVariable Long id) {

    Student student = studentService.findById(id);

    return ResponseEntity.ok(student);

}
```

This allows you to customize:

- Status Code
- Headers
- Body

---

# Returning Custom Status Codes

Example:

```java
@PostMapping
public ResponseEntity<Student> createStudent(
        @RequestBody Student student) {

    Student saved = studentService.save(student);

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(saved);

}
```

Response:

```
201 Created
```

---

# Returning No Content

When deleting resources:

```java
@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteStudent(
        @PathVariable Long id) {

    studentService.delete(id);

    return ResponseEntity.noContent().build();

}
```

Response:

```
204 No Content
```

---

# Request and Response Flow

```
Client

↓

HTTP Request

↓

DispatcherServlet

↓

Controller

↓

Student Object

↓

Jackson

↓

HTTP Response

↓

Client
```

Most REST APIs follow this exact flow.

---

# Common Mistakes

❌ Returning incorrect HTTP status codes.

❌ Writing JSON manually.

❌ Ignoring request headers.

❌ Returning `null` instead of meaningful responses.

❌ Returning stack traces to clients.

---

# Best Practices

- Use `ResponseEntity` for flexible responses.
- Return appropriate status codes.
- Let Spring handle JSON conversion.
- Keep controllers simple.
- Validate requests before processing.
- Return meaningful error responses.

---

# Industry Insight

Modern REST APIs rarely interact directly with `HttpServletRequest` and `HttpServletResponse`.

Instead, they rely on Spring's annotation-based programming model:

- `@PathVariable`
- `@RequestParam`
- `@RequestBody`
- `ResponseEntity`

Direct access to servlet objects is typically reserved for advanced scenarios such as custom filters, authentication, logging, or request inspection.

---

# Interview Corner

## Basic

1. What is an HTTP Request?
2. What is an HTTP Response?
3. What is `HttpServletRequest`?

---

## Intermediate

4. What is `ResponseEntity`?
5. Why should APIs return proper HTTP status codes?
6. How does Spring convert Java objects into JSON?

---

## Advanced

7. When should you use `HttpServletRequest` directly?
8. Explain the request-response lifecycle in Spring MVC.
9. Why is `ResponseEntity` preferred over returning plain objects in many APIs?

---

# Hands-on Lab

## Objective

Create endpoints that demonstrate request and response handling.

### Tasks

1. Create a GET endpoint that returns a `Student`.
2. Read the `User-Agent` header.
3. Return `201 Created` for a POST request.
4. Return `204 No Content` for a DELETE request.
5. Test all endpoints using Postman or IntelliJ HTTP Client.
6. Observe the response headers and status codes.

---

# Cheat Sheet

- HTTP Request = Method + URL + Headers + Body.
- HTTP Response = Status + Headers + Body.
- `HttpServletRequest` provides access to incoming requests.
- `HttpServletResponse` represents outgoing responses.
- `ResponseEntity` offers complete control over HTTP responses.
- Spring Boot automatically converts Java objects into JSON using Jackson.

---

# Summary

HTTP request and response handling is the foundation of every web application. Spring MVC simplifies this process by automatically creating request and response objects, routing requests to controllers, and converting Java objects into JSON responses. By using `ResponseEntity`, proper HTTP status codes, and Spring's automatic serialization, developers can build professional, standards-compliant REST APIs that are easy to consume and maintain.

---

# What's Next?

➡ **Chapter 6 – Path Variables and Request Parameters**

In the next chapter, you'll learn how to capture dynamic values from URLs using `@PathVariable`, read query parameters with `@RequestParam`, and design flexible REST endpoints that accept user input in a clean and RESTful way.
