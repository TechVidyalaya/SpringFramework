---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 7
title: Request Body and Response Body
difficulty: Intermediate
estimated_reading_time: 65 Minutes
estimated_coding_time: 45 Minutes
estimated_lab_time: 35 Minutes
version: 1.0
---

# Chapter 7
# Request Body and Response Body

> **"Modern APIs don't exchange HTML—they exchange data. In Spring MVC, `@RequestBody` and `@ResponseBody` make that communication effortless."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what Request Body and Response Body are.
- Learn how `@RequestBody` converts JSON into Java objects.
- Understand how `@ResponseBody` converts Java objects into JSON.
- Explore the role of Jackson in serialization and deserialization.
- Understand Content-Type and Accept headers.
- Follow the complete JSON request-response lifecycle in Spring MVC.

---

# Introduction

Suppose a frontend application wants to register a new student.

It sends the following request:

```http
POST /students
Content-Type: application/json

{
    "name": "Rahul Sharma",
    "email": "rahul@example.com",
    "department": "CSE"
}
```

Inside our controller, we want to receive this JSON as a Java object.

Similarly, after saving the student, we want to return:

```json
{
    "id": 101,
    "name": "Rahul Sharma",
    "email": "rahul@example.com",
    "department": "CSE"
}
```

How does Spring perform these conversions automatically?

The answer lies in two important annotations:

- `@RequestBody`
- `@ResponseBody`

---

# Understanding the HTTP Message Body

Every HTTP request consists of several parts.

```
HTTP Request

↓

Request Line

↓

Headers

↓

Body (Optional)
```

The **Body** contains the actual data sent by the client.

Example:

```http
POST /students

Content-Type: application/json

{
    "name":"Rahul"
}
```

For `GET` requests, the body is usually empty.

For `POST`, `PUT`, and `PATCH`, the body commonly contains JSON data.

---

# What is @RequestBody?

`@RequestBody` tells Spring:

> "Read the HTTP request body and convert it into a Java object."

Example:

```java
@PostMapping
public Student createStudent(
        @RequestBody Student student) {

    return studentService.save(student);

}
```

Spring automatically converts the JSON request into a `Student` object.

---

# Request Flow with @RequestBody

```
Client

↓

JSON Request

↓

DispatcherServlet

↓

Http Message Converter

↓

Student Object

↓

Controller
```

This process is called **Deserialization**.

---

# Example Request

Client sends:

```json
{
    "name": "Rahul",
    "email": "rahul@example.com",
    "department": "CSE"
}
```

Spring creates:

```java
Student student = new Student();

student.setName("Rahul");

student.setEmail("rahul@example.com");

student.setDepartment("CSE");
```

All of this happens automatically.

---

# What is Deserialization?

Deserialization is the process of converting structured data (such as JSON) into Java objects.

```
JSON

↓

Jackson

↓

Java Object
```

Spring Boot performs this conversion using the Jackson library.

---

# Student Model

Example:

```java
public class Student {

    private Long id;

    private String name;

    private String email;

    private String department;

    // Getters and Setters

}
```

Jackson maps JSON fields to Java fields based on their names.

---

# What is @ResponseBody?

`@ResponseBody` performs the opposite operation.

It tells Spring:

> "Convert the returned Java object into the HTTP response body."

Example:

```java
@GetMapping("/{id}")
@ResponseBody
public Student getStudent(
        @PathVariable Long id) {

    return studentService.findById(id);

}
```

The returned object becomes a JSON response.

---

# Response Flow

```
Student Object

↓

Jackson

↓

JSON

↓

HTTP Response

↓

Client
```

This process is called **Serialization**.

---

# What is Serialization?

Serialization converts Java objects into structured data.

```
Java Object

↓

Jackson

↓

JSON
```

The JSON is written into the HTTP response body.

---

# @RestController and @ResponseBody

Earlier, we learned about `@RestController`.

Internally:

```java
@RestController
```

is equivalent to:

```java
@Controller

@ResponseBody
```

This means every method automatically returns JSON unless configured otherwise.

---

# Understanding HttpMessageConverter

Spring uses **HttpMessageConverter** to convert data between HTTP messages and Java objects.

```
JSON Request

↓

HttpMessageConverter

↓

Java Object

↓

Controller

↓

Java Object

↓

HttpMessageConverter

↓

JSON Response
```

You rarely interact with this class directly, but it powers every REST API.

---

# Jackson – The JSON Engine

Spring Boot automatically includes the Jackson library through the Spring Web starter.

Jackson is responsible for:

- JSON → Java Object
- Java Object → JSON

You don't need to write conversion logic yourself.

---

# Content-Type Header

The client tells the server what it is sending using the `Content-Type` header.

Example:

```http
Content-Type: application/json
```

Other common values include:

| Content-Type | Purpose |
|--------------|---------|
| `application/json` | JSON data |
| `application/xml` | XML data |
| `text/plain` | Plain text |
| `multipart/form-data` | File uploads |

For REST APIs, `application/json` is the most common.

---

# Accept Header

The client can also specify the response format.

Example:

```http
Accept: application/json
```

Meaning:

> "Please return the response in JSON format."

---

# Complete Example

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @PostMapping
    public Student createStudent(
            @RequestBody Student student) {

        return studentService.save(student);

    }

    @GetMapping("/{id}")
    public Student getStudent(
            @PathVariable Long id) {

        return studentService.findById(id);

    }

}
```

Request:

```http
POST /students

Content-Type: application/json

{
    "name":"Rahul",
    "email":"rahul@example.com",
    "department":"CSE"
}
```

Response:

```json
{
    "id":101,
    "name":"Rahul",
    "email":"rahul@example.com",
    "department":"CSE"
}
```

---

# Complete Request-Response Lifecycle

```
Frontend

↓

HTTP Request

↓

JSON

↓

DispatcherServlet

↓

HttpMessageConverter

↓

Student Object

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

Student Object

↓

HttpMessageConverter

↓

JSON

↓

HTTP Response

↓

Frontend
```

This is the lifecycle followed by nearly every Spring Boot REST API.

---

# Common Mistakes

❌ Forgetting to use `@RequestBody`.

❌ Returning manually constructed JSON strings.

❌ Sending incorrect `Content-Type` headers.

❌ Using mismatched JSON property names.

❌ Returning entities with sensitive information (e.g., passwords).

---

# Best Practices

- Use DTOs (Data Transfer Objects) instead of exposing entities directly.
- Always send `Content-Type: application/json` for JSON payloads.
- Use `@RestController` for REST APIs.
- Let Jackson handle serialization and deserialization.
- Validate incoming request bodies before processing.

---

# Industry Insight

In enterprise applications, controllers rarely work directly with entities.

Instead, the flow is:

```
JSON Request

↓

Request DTO

↓

Service

↓

Entity

↓

Database

↓

Entity

↓

Response DTO

↓

JSON Response
```

Using separate DTOs improves security, flexibility, and maintainability by preventing internal database structures from being exposed to API consumers.

---

# Interview Corner

## Basic

1. What is `@RequestBody`?
2. What is `@ResponseBody`?
3. What is serialization?

---

## Intermediate

4. What is deserialization?
5. What is `HttpMessageConverter`?
6. What role does Jackson play in Spring Boot?

---

## Advanced

7. Why is `@RestController` preferred over `@Controller` for REST APIs?
8. Why are DTOs preferred over entities in request and response bodies?
9. Explain the complete JSON request-response lifecycle in Spring MVC.

---

# Hands-on Lab

## Objective

Build endpoints that receive and return JSON.

### Tasks

1. Create a `Student` model.
2. Create a POST endpoint using `@RequestBody`.
3. Create a GET endpoint returning a `Student`.
4. Send a JSON request using Postman.
5. Observe the JSON response.
6. Change the `Content-Type` header and observe the application's behavior.

---

# Cheat Sheet

- `@RequestBody` converts JSON into Java objects.
- `@ResponseBody` converts Java objects into JSON.
- `@RestController` automatically applies `@ResponseBody`.
- Jackson performs serialization and deserialization.
- `Content-Type` describes the request payload.
- `Accept` specifies the desired response format.
- `HttpMessageConverter` performs the data conversion behind the scenes.

---

# Summary

Request and Response Bodies are the foundation of data exchange in modern REST APIs. Spring MVC simplifies this process through `@RequestBody` and `@ResponseBody`, automatically converting between JSON and Java objects using Jackson and `HttpMessageConverter`. By understanding serialization, deserialization, HTTP headers, and content negotiation, developers can build clean, reliable, and standards-compliant APIs that integrate seamlessly with web, mobile, and enterprise applications.

---

# What's Next?

➡ **Chapter 8 – Building REST APIs**

In the next chapter, you'll combine everything you've learned so far to build a complete RESTful API for the Student Management System, following REST principles, HTTP standards, and industry best practices.
