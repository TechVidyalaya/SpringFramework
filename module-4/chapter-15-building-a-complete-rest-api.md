---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 15
title: Building a Complete REST API (Capstone)
difficulty: Intermediate
estimated_reading_time: 90 Minutes
estimated_coding_time: 120 Minutes
estimated_lab_time: 90 Minutes
version: 1.0
---

# Chapter 15

# Building a Complete REST API (Capstone)

> **"A professional REST API is not a collection of endpoints—it's a well-designed system where every layer has a clear responsibility."**

---

# 🎯 Learning Objectives

After completing this chapter, students will be able to:

- Build a complete REST API using Spring Boot
- Design production-ready REST endpoints
- Apply DTO architecture
- Validate requests
- Handle exceptions globally
- Upload and download files
- Configure CORS
- Add OpenAPI documentation
- Use Filters and Interceptors
- Return consistent HTTP responses
- Follow enterprise coding standards

---

# Project Overview

The Student Management REST API includes:

- Students
- Courses
- Teachers
- Departments
- Profile Photos

---

# Final Architecture

```text
Browser / Mobile

        │
        ▼
REST Controller
        │
        ▼
Service Layer
        │
        ▼
Repository Layer
        │
        ▼
Database
```

Supporting components:

```text
Filters
↓
Interceptors
↓
Global Exception Handler
↓
DTO Mapper
↓
Validation
↓
OpenAPI
↓
CORS
↓
File Storage
```

---

# Project Structure

```text
src/main/java
└── com.techvidyalaya.student
    ├── config
    │   ├── OpenApiConfig
    │   └── WebConfig
    ├── controller
    │   └── StudentController
    ├── dto
    │   ├── StudentRequest
    │   ├── StudentResponse
    │   ├── ApiErrorResponse
    │   └── ValidationErrorResponse
    ├── entity
    │   └── Student
    ├── service
    │   ├── StudentService
    │   └── StudentServiceImpl
    ├── repository
    │   └── StudentRepository
    ├── exception
    │   ├── StudentNotFoundException
    │   └── GlobalExceptionHandler
    ├── interceptor
    ├── filter
    ├── storage
    └── StudentManagementApplication
```

---

# API Endpoints

## Student APIs

```text
GET    /students
GET    /students/{id}
POST   /students
PUT    /students/{id}
DELETE /students/{id}
```

## File APIs

```text
POST /students/{id}/profile-photo
GET  /students/{id}/profile-photo
```

---

# End-to-End Request Flow

```text
Client
↓
Filter
↓
DispatcherServlet
↓
Interceptor
↓
Controller
↓
Validation
↓
Service
↓
Repository
↓
Database
↓
Response DTO
↓
Exception Handler
↓
Client
```

---

# DTO Design

## StudentRequest

Fields:

- name
- email
- department
- dateOfBirth

Apply Bean Validation annotations.

## StudentResponse

Fields:

- id
- name
- email
- department
- createdAt

---

# Standard Error Response

```text
timestamp
status
error
message
path
```

Validation response additionally contains:

```text
fieldErrors
```

---

# HTTP Status Codes

| Operation | Status |
|-----------|--------|
| GET | 200 OK |
| POST | 201 Created |
| PUT | 200 OK |
| DELETE | 204 No Content |
| Validation Failure | 400 Bad Request |
| Resource Not Found | 404 Not Found |
| Conflict | 409 Conflict |
| Unexpected Error | 500 Internal Server Error |

---

# CORS

Allow requests from:

```text
http://localhost:4200
```

---

# OpenAPI

Document:

- Endpoints
- DTOs
- Validation
- Responses
- Examples

---

# Logging

Implement:

```text
LoggingFilter

↓

LoggingInterceptor
```

---

# File Upload

Requirements:

- PNG
- JPEG
- Maximum size: 5 MB

---

# Production Checklist

- Layered architecture
- DTOs instead of entities
- Bean Validation
- Global exception handling
- Standard error responses
- Logging
- CORS
- Swagger/OpenAPI
- File upload support
- Consistent HTTP status codes
- Constructor injection
- Clean package structure

---

# End-to-End Demonstration

Complete the following workflow:

1. Retrieve all students.
2. Retrieve a student by ID.
3. Create a valid student.
4. Submit an invalid student request.
5. Update a student.
6. Delete a student.
7. Upload a profile photo.
8. Download the profile photo.
9. Verify request logging.
10. Test CORS from an Angular application.
11. Explore the API using Swagger UI.

---

# Interview Corner

Be prepared to answer:

- Explain the architecture of your Student Management API.
- Why use DTOs instead of entities?
- How does Bean Validation work?
- How are exceptions handled globally?
- Explain the complete request lifecycle.
- How does OpenAPI generate documentation?
- Why is CORS required?
- Difference between Filters and Interceptors?
- How would you secure this API?
- How would you migrate this application to microservices?

---

# Hands-on Challenge

Extend the application by implementing:

- Course Management APIs
- Teacher Management APIs
- Student search with pagination and sorting
- Profile photo replacement
- Soft delete
- Audit logging
- API versioning (/api/v2)
- Docker containerisation

---

# Cheat Sheet

- Use layered architecture.
- Keep controllers thin.
- Place business logic in services.
- Validate request DTOs.
- Use global exception handling.
- Return consistent HTTP status codes.
- Store files outside the application source directory.
- Enable CORS only for trusted origins.
- Document APIs with OpenAPI.
- Log requests with Filters and Interceptors.

---

# Summary

This capstone chapter brings together everything learned throughout Module 4 into a single production-ready Student Management REST API. The application combines REST controllers, DTOs, Bean Validation, global exception handling, file upload and download, Filters, Interceptors, CORS, and OpenAPI documentation using a clean layered architecture.

This project serves as the foundation for the next module, where in-memory data handling will be replaced with persistent storage using Spring Data JPA.

---

# What's Next?

➡ Module 5 – Spring Data JPA

In the next module, you'll connect this REST API to a relational database using Spring Data JPA, Hibernate, repositories, entity relationships, transactions, and query methods.

