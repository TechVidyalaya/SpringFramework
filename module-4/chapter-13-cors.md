---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 13
title: Cross-Origin Resource Sharing (CORS)
difficulty: Intermediate
estimated_reading_time: 70 Minutes
estimated_coding_time: 45 Minutes
estimated_lab_time: 35 Minutes
version: 1.0
---

# Chapter 13
# Cross-Origin Resource Sharing (CORS)

> **"Your API may be running perfectly, but if the browser blocks the request, your users will never know."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what an Origin is.
- Learn why browsers block cross-origin requests.
- Understand the Same-Origin Policy (SOP).
- Learn how CORS works.
- Configure CORS in Spring Boot.
- Handle preflight requests.
- Follow enterprise security best practices.

---

# Introduction

Suppose your Student Management System has:

Backend:

```
http://localhost:8080
```

Frontend:

```
http://localhost:4200
```

Your Angular application sends a request:

```http
GET http://localhost:8080/students
```

Instead of receiving student data, the browser displays:

```
Access to XMLHttpRequest has been blocked by CORS policy.
```

The backend is running.

The endpoint exists.

The API works.

So why is the browser blocking it?

To answer that, we first need to understand the **Same-Origin Policy**.

---

# What is an Origin?

An **Origin** consists of three parts:

- Protocol
- Host
- Port

Example:

```
http://localhost:8080
│      │          │
│      │          └── Port
│      └───────────── Host
└──────────────────── Protocol
```

All three must match for two URLs to have the same origin.

---

# Examples

| URL | Same Origin? |
|------|--------------|
| http://localhost:8080 | Original |
| http://localhost:4200 | ❌ Different Port |
| https://localhost:8080 | ❌ Different Protocol |
| http://api.example.com | ❌ Different Host |
| http://localhost:8080/students | ✅ Same Origin |

Even a different port creates a different origin.

---

# What is the Same-Origin Policy?

The **Same-Origin Policy (SOP)** is a browser security feature.

It prevents a webpage from freely accessing resources from another origin.

Example:

```
Frontend

http://localhost:4200

↓

Backend

http://localhost:8080
```

Since the ports differ, the browser blocks the request unless permission is granted.

---

# Why Does the Browser Block Requests?

Imagine visiting a malicious website.

Without SOP, that site could silently send requests to:

- Your banking application
- Your email account
- Government websites
- Internal company portals

The browser blocks such requests to protect users.

---

# Important Point

The restriction is enforced by the **browser**, not Spring Boot.

```
Browser

↓

Checks Origin

↓

Allowed?

↓

Yes → Send Response

No → Block Response
```

Tools like Postman or cURL are **not** affected because they are not browsers.

---

# What is CORS?

**CORS (Cross-Origin Resource Sharing)** is a mechanism that allows a server to tell the browser:

> "This origin is allowed to access my resources."

The browser then decides whether to allow the response.

---

# CORS Workflow

```
Angular Application

↓

HTTP Request

↓

Spring Boot API

↓

CORS Headers

↓

Browser

↓

Allowed?

↓

Response
```

The browser checks the response headers before exposing the response to JavaScript.

---

# CORS Response Headers

A typical response includes:

```http
Access-Control-Allow-Origin:
http://localhost:4200
```

This header tells the browser that requests from Angular are allowed.

Other common headers include:

```http
Access-Control-Allow-Methods

Access-Control-Allow-Headers

Access-Control-Allow-Credentials
```

---

# Enabling CORS on a Controller

Spring Boot provides the `@CrossOrigin` annotation.

Example:

```java
@RestController
@RequestMapping("/students")

@CrossOrigin(
        origins = "http://localhost:4200")
public class StudentController {

}
```

Now requests from Angular are accepted.

---

# Allowing Multiple Origins

Example:

```java
@CrossOrigin(
    origins = {
        "http://localhost:4200",
        "http://localhost:3000"
    }
)
```

Useful when supporting multiple frontend applications.

---

# Configuring CORS for a Single Endpoint

Example:

```java
@GetMapping("/{id}")

@CrossOrigin(
        origins = "http://localhost:4200")
public StudentResponse getStudent(
        @PathVariable Long id) {

    return studentService.findById(id);

}
```

Only this endpoint accepts cross-origin requests.

---

# Global CORS Configuration

Instead of annotating every controller, configure CORS globally.

```java
@Configuration
public class WebConfig
        implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(
            CorsRegistry registry) {

        registry.addMapping("/**")
                .allowedOrigins(
                        "http://localhost:4200")
                .allowedMethods(
                        "GET",
                        "POST",
                        "PUT",
                        "DELETE");

    }

}
```

This applies to the entire application.

---

# What is a Preflight Request?

Some requests require permission before the actual request is sent.

The browser first sends an **OPTIONS** request.

```
Browser

↓

OPTIONS

↓

Server

↓

Permission Granted?

↓

Actual Request
```

This is called a **Preflight Request**.

---

# When Does Preflight Occur?

Typically when:

- Using PUT
- Using DELETE
- Using PATCH
- Sending custom headers
- Sending credentials
- Using non-standard content types

Simple GET requests usually do not require preflight.

---

# Preflight Example

Browser sends:

```http
OPTIONS /students

Origin:
http://localhost:4200

Access-Control-Request-Method:
POST
```

Server responds:

```http
Access-Control-Allow-Origin:
http://localhost:4200

Access-Control-Allow-Methods:
POST
```

The browser then sends the actual POST request.

---

# Allowing HTTP Methods

Example:

```java
.allowedMethods(
        "GET",
        "POST",
        "PUT",
        "PATCH",
        "DELETE")
```

Only these methods are accepted from cross-origin requests.

---

# Allowing Headers

Some APIs require custom headers.

Example:

```java
.allowedHeaders("*")
```

Or:

```java
.allowedHeaders(
        "Authorization",
        "Content-Type")
```

---

# Credentials

Applications using cookies or authentication tokens may need:

```java
.allowCredentials(true)
```

When credentials are enabled:

- Wildcard (`*`) origins are not allowed.
- Specific origins must be configured.

---

# Complete Global Configuration

```java
@Configuration
public class WebConfig
        implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(
            CorsRegistry registry) {

        registry.addMapping("/**")

                .allowedOrigins(
                        "http://localhost:4200")

                .allowedMethods(
                        "GET",
                        "POST",
                        "PUT",
                        "DELETE")

                .allowedHeaders("*")

                .allowCredentials(true);

    }

}
```

---

# CORS Request Lifecycle

```
Browser

↓

Cross-Origin Request

↓

Preflight (Optional)

↓

Spring Boot

↓

CORS Validation

↓

Controller

↓

Response Headers

↓

Browser

↓

JavaScript
```

---

# Development vs Production

Development:

```
Angular

↓

localhost:4200

↓

Spring Boot

↓

localhost:8080
```

Production:

```
Frontend

↓

https://student.example.com

↓

Backend

↓

https://api.example.com
```

Production applications should explicitly allow trusted domains.

---

# Common Mistakes

❌ Allowing every origin using `"*"` in production.

❌ Forgetting to allow required HTTP methods.

❌ Ignoring preflight requests.

❌ Allowing credentials with wildcard origins.

❌ Assuming Postman tests CORS.

---

# Best Practices

- Allow only trusted origins.
- Prefer global configuration for consistency.
- Keep CORS configuration environment-specific.
- Avoid wildcard origins in production.
- Limit allowed methods and headers.
- Test using a real browser.

---

# Industry Insight

Large organisations often expose APIs to:

- Web applications
- Mobile applications
- Third-party partners
- Internal portals

Each client is given access through carefully controlled CORS policies.

In many organisations, CORS settings are managed together with API gateways and security policies rather than individually within each service.

---

# Interview Corner

## Basic

1. What is CORS?
2. What is an Origin?
3. What is the Same-Origin Policy?

---

## Intermediate

4. Why does the browser perform a preflight request?
5. What is the purpose of `@CrossOrigin`?
6. Why doesn't Postman encounter CORS errors?

---

## Advanced

7. Explain the complete CORS request lifecycle.
8. Why should wildcard origins be avoided in production?
9. Explain the relationship between CORS and browser security.

---

# Hands-on Lab

## Objective

Allow an Angular frontend to communicate with the Student Management System.

### Tasks

1. Create an Angular application running on:

```
http://localhost:4200
```

2. Call:

```
GET /students
```

3. Observe the browser's CORS error.

4. Add `@CrossOrigin`.

5. Verify that the request succeeds.

6. Move the configuration into `WebConfig`.

7. Add support for:

- GET
- POST
- PUT
- DELETE

8. Test a POST request that triggers a preflight request.

---

# Cheat Sheet

- Origin = Protocol + Host + Port.
- Browsers enforce the Same-Origin Policy.
- CORS allows approved cross-origin requests.
- `@CrossOrigin` enables CORS on controllers or methods.
- `WebMvcConfigurer` enables global CORS configuration.
- Some requests require an OPTIONS preflight request.
- Configure only trusted origins in production.
- Postman does not enforce CORS.

---

# Summary

CORS is a browser security mechanism that controls how web applications communicate across different origins. While the Same-Origin Policy protects users from malicious websites, CORS provides a safe way for servers to grant access to trusted clients. Spring Boot makes CORS configuration straightforward through `@CrossOrigin` and global MVC configuration. Properly configured CORS policies improve security while enabling modern frontend frameworks such as Angular, React, and Vue to communicate seamlessly with Spring Boot APIs.

---

# What's Next?

➡ **Chapter 14 – API Documentation with OpenAPI (Swagger)**

In the next chapter, you'll learn how to automatically generate interactive REST API documentation using **OpenAPI** and **Swagger UI**, making your APIs easier to understand, test, and integrate with.
