---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 10
title: Global Exception Handling
difficulty: Intermediate
estimated_reading_time: 75 Minutes
estimated_coding_time: 60 Minutes
estimated_lab_time: 40 Minutes
version: 1.0
---

# Chapter 10
# Global Exception Handling

> **"Errors are inevitable. Professional applications don't avoid exceptions—they handle them gracefully."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what exceptions are and why they occur.
- Learn how Spring MVC handles exceptions.
- Create global exception handlers using `@RestControllerAdvice`.
- Handle validation errors consistently.
- Design standardized API error responses.
- Follow enterprise best practices for exception handling.

---

# Introduction

Imagine a client requests a student that doesn't exist.

```http
GET /students/9999
```

Your service tries to find the student but nothing is returned.

Without proper exception handling, the client may receive:

```http
500 Internal Server Error
```

or even worse,

a long stack trace exposing internal implementation details.

Professional APIs never expose internal exceptions directly.

Instead, they return clear, consistent, and meaningful error responses.

---

# What is an Exception?

An **Exception** is an event that interrupts the normal execution of a program.

Examples:

- Student not found
- Invalid input
- Database unavailable
- Null reference
- Divide by zero
- File not found

Exceptions indicate that something unexpected has happened.

---

# Exception Flow

```
Client Request

↓

Controller

↓

Service

↓

Exception Thrown

↓

Exception Handler

↓

Error Response

↓

Client
```

Instead of crashing the application, Spring allows us to convert exceptions into structured HTTP responses.

---

# Types of Exceptions

Exceptions generally fall into two categories.

## Checked Exceptions

Checked exceptions must be handled or declared.

Examples:

- IOException
- SQLException

---

## Unchecked Exceptions

Unchecked exceptions extend `RuntimeException`.

Examples:

- NullPointerException
- IllegalArgumentException
- IllegalStateException

Most custom business exceptions extend `RuntimeException`.

---

# What Happens Without Exception Handling?

Suppose the service throws:

```java
throw new RuntimeException("Student not found");
```

Without an exception handler, Spring returns:

```http
500 Internal Server Error
```

This is not helpful to API consumers.

---

# Local Exception Handling

A controller can handle its own exceptions.

Example:

```java
@ExceptionHandler(StudentNotFoundException.class)
public ResponseEntity<String> handleException(
        StudentNotFoundException ex) {

    return ResponseEntity
            .status(HttpStatus.NOT_FOUND)
            .body(ex.getMessage());

}
```

However, duplicating this logic across every controller quickly becomes difficult to maintain.

---

# Why Global Exception Handling?

Instead of handling exceptions in every controller,

Spring allows us to create one centralized exception handler.

```
Controller A

↓

Controller B

↓

Controller C

↓

Global Exception Handler

↓

HTTP Response
```

This approach is cleaner, reusable, and consistent.

---

# What is @RestControllerAdvice?

`@RestControllerAdvice` is a specialized annotation that applies exception handling across all REST controllers.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

}
```

Whenever an exception occurs, Spring checks this class for an appropriate handler.

---

# Using @ExceptionHandler

Inside the advice class, define methods for handling specific exceptions.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<String> handleStudentNotFound(
            StudentNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());

    }

}
```

---

# Creating a Custom Exception

Business exceptions should have meaningful names.

Example:

```java
public class StudentNotFoundException
        extends RuntimeException {

    public StudentNotFoundException(
            String message) {

        super(message);

    }

}
```

Throw it when appropriate.

```java
throw new StudentNotFoundException(
        "Student not found with id: " + id);
```

---

# Handling Multiple Exceptions

A single class can handle many exceptions.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<String> handleStudentNotFound(
            StudentNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());

    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneralException(
            Exception ex) {

        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Something went wrong.");

    }

}
```

The generic handler acts as a fallback.

---

# Handling Validation Errors

In the previous chapter, we learned that invalid requests generate validation exceptions.

Example request:

```json
{
    "name": "",
    "email": "invalid"
}
```

Spring throws:

```
MethodArgumentNotValidException
```

We can customize its response.

---

# Handling Validation Exceptions

Example:

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<String> handleValidation(
        MethodArgumentNotValidException ex) {

    return ResponseEntity
            .badRequest()
            .body("Validation failed.");

}
```

A more detailed response is even better.

---

# Standard Error Response

Enterprise APIs usually return structured error objects.

Example:

```json
{
    "timestamp": "2026-07-24T14:30:00",
    "status": 404,
    "error": "Not Found",
    "message": "Student not found with id: 101",
    "path": "/students/101"
}
```

This format is easy for frontend applications to process.

---

# Creating an Error Response DTO

```java
public class ErrorResponse {

    private LocalDateTime timestamp;

    private int status;

    private String error;

    private String message;

    private String path;

}
```

Instead of returning plain strings, controllers return this DTO.

---

# Returning ErrorResponse

```java
@ExceptionHandler(StudentNotFoundException.class)
public ResponseEntity<ErrorResponse> handleStudentNotFound(
        StudentNotFoundException ex,
        HttpServletRequest request) {

    ErrorResponse error = new ErrorResponse();

    error.setTimestamp(LocalDateTime.now());

    error.setStatus(404);

    error.setError("Not Found");

    error.setMessage(ex.getMessage());

    error.setPath(request.getRequestURI());

    return ResponseEntity
            .status(HttpStatus.NOT_FOUND)
            .body(error);

}
```

This produces consistent responses across the application.

---

# Exception Handling Flow

```
Client

↓

Controller

↓

Service

↓

Exception

↓

@RestControllerAdvice

↓

ErrorResponse DTO

↓

JSON Response

↓

Client
```

Every exception follows this centralized flow.

---

# Mapping Exceptions to Status Codes

| Exception | HTTP Status |
|------------|-------------|
| StudentNotFoundException | 404 Not Found |
| IllegalArgumentException | 400 Bad Request |
| MethodArgumentNotValidException | 400 Bad Request |
| AccessDeniedException | 403 Forbidden |
| Exception | 500 Internal Server Error |

Using appropriate status codes helps API consumers understand the problem.

---

# Logging Exceptions

Exception handlers should log errors before returning responses.

Example:

```java
private static final Logger logger =
        LoggerFactory.getLogger(
                GlobalExceptionHandler.class);

logger.error("Student not found", ex);
```

Logging helps diagnose production issues while keeping responses user-friendly.

---

# What Should Never Be Returned?

Never expose:

- Stack traces
- Database queries
- Internal class names
- Passwords
- API keys
- File system paths

Example of a bad response:

```text
java.lang.NullPointerException
    at com.company.service.StudentService...
```

Such responses reveal internal implementation details and create security risks.

---

# Common Mistakes

❌ Returning raw exception messages.

❌ Duplicating exception handling across controllers.

❌ Ignoring validation exceptions.

❌ Returning `500 Internal Server Error` for every problem.

❌ Exposing sensitive implementation details.

---

# Best Practices

- Use `@RestControllerAdvice` for centralized handling.
- Create custom business exceptions.
- Return structured error responses.
- Map exceptions to meaningful HTTP status codes.
- Log exceptions internally.
- Never expose stack traces to clients.

---

# Industry Insight

Large enterprise systems may expose hundreds of APIs.

To ensure consistency, they define a **single error response format** that every service follows.

Benefits include:

- Easier frontend integration
- Consistent client experience
- Simpler debugging
- Better API documentation
- Improved observability

Many organizations publish these error formats as part of their API standards.

---

# Interview Corner

## Basic

1. What is an exception?
2. What is `@ExceptionHandler`?
3. What is `@RestControllerAdvice`?

---

## Intermediate

4. Why is global exception handling preferred over local handling?
5. How do you create a custom exception?
6. How should validation exceptions be handled?

---

## Advanced

7. Design a standard API error response.
8. Why should stack traces never be exposed to API consumers?
9. Explain the exception handling lifecycle in Spring MVC.

---

# Hands-on Lab

## Objective

Implement centralized exception handling for the Student Management System.

### Tasks

1. Create a `StudentNotFoundException`.
2. Create an `ErrorResponse` DTO.
3. Create a `GlobalExceptionHandler` using `@RestControllerAdvice`.
4. Handle:
   - Student not found
   - Validation failures
   - Generic exceptions
5. Return meaningful HTTP status codes.
6. Test the responses using Postman or IntelliJ HTTP Client.

---

# Cheat Sheet

- Exceptions interrupt normal program execution.
- Use custom exceptions for business scenarios.
- `@RestControllerAdvice` centralizes exception handling.
- `@ExceptionHandler` maps exceptions to handler methods.
- Return structured error responses.
- Log exceptions internally.
- Never expose implementation details.

---

# Summary

Exception handling is a critical aspect of building reliable REST APIs. By centralizing exception handling with `@RestControllerAdvice`, creating meaningful custom exceptions, and returning standardized error responses, Spring Boot applications become easier to maintain, more secure, and more user-friendly. A consistent error-handling strategy improves both developer experience and API usability while preventing sensitive internal details from being exposed.

---

# What's Next?

➡ **Chapter 11 – File Upload and Download**

In the next chapter, you'll learn how to handle file uploads and downloads in Spring Boot using `MultipartFile`, serve files securely to clients, and build features such as profile photo uploads, document management, and report downloads for the Student Management System.
