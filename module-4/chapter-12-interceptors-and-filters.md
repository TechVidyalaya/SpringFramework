---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 12
title: Interceptors and Filters
difficulty: Intermediate
estimated_reading_time: 80 Minutes
estimated_coding_time: 60 Minutes
estimated_lab_time: 45 Minutes
version: 1.0
---

# Chapter 12
# Interceptors and Filters

> **"Not every request should go directly to your controller. Sometimes it must be inspected, modified, authenticated, or logged first."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand why Filters and Interceptors exist.
- Learn the request lifecycle in Spring MVC.
- Differentiate between Servlet Filters and Spring Interceptors.
- Implement custom Filters.
- Implement custom Interceptors.
- Understand real-world use cases.
- Follow enterprise best practices.

---

# Introduction

Imagine a student accesses the following endpoint:

```http
GET /students/101
```

Before the request reaches the controller, an enterprise application may need to:

- Verify authentication
- Log the request
- Record execution time
- Check API keys
- Add security headers
- Validate rate limits

Should every controller implement these tasks?

**Absolutely not.**

These are called **cross-cutting concerns**, and they should be handled outside the business logic.

Spring and the Servlet framework provide two mechanisms for this:

- **Filters**
- **Interceptors**

---

# The Request Journey

A request passes through several stages before reaching your controller.

```
Client

↓

Servlet Filter

↓

DispatcherServlet

↓

Spring Interceptor

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

Spring Interceptor

↓

Servlet Filter

↓

Client
```

Understanding where Filters and Interceptors execute is the key to knowing when to use each one.

---

# Cross-Cutting Concerns

Cross-cutting concerns are features shared across many parts of an application.

Examples include:

- Authentication
- Authorization
- Logging
- Monitoring
- Auditing
- Compression
- Security headers
- Request tracing
- Performance measurement

Instead of duplicating code in every controller, these concerns are handled centrally.

---

# What is a Filter?

A **Filter** is part of the **Servlet API**.

It processes every HTTP request **before Spring MVC begins handling it**.

```
Browser

↓

Tomcat

↓

Filter

↓

DispatcherServlet

↓

Controller
```

Filters are framework-independent and operate at the servlet container level.

---

# Common Uses of Filters

Filters are commonly used for:

- Logging
- CORS
- Compression
- Character encoding
- Authentication
- Request wrapping
- Security headers

Since Filters execute before Spring MVC, they can inspect or modify every request.

---

# Creating a Filter

Example:

```java
@Component
public class LoggingFilter
        implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException,
                   ServletException {

        System.out.println("Incoming Request");

        chain.doFilter(request, response);

    }

}
```

The request continues only after calling:

```java
chain.doFilter(request, response);
```

---

# Filter Lifecycle

```
Client

↓

Filter

↓

DispatcherServlet

↓

Controller

↓

Response

↓

Filter

↓

Client
```

Filters execute both before and after request processing.

---

# What is an Interceptor?

An **Interceptor** is a Spring MVC component.

It executes **after the DispatcherServlet but before the Controller**.

```
DispatcherServlet

↓

Interceptor

↓

Controller
```

Unlike Filters, Interceptors have access to Spring MVC concepts such as handlers and controller methods.

---

# Common Uses of Interceptors

Interceptors are frequently used for:

- Authentication
- Authorization
- Execution timing
- Audit logging
- Locale selection
- User activity tracking
- Request preprocessing

---

# Interceptor Lifecycle

A Spring Interceptor provides three callback methods.

```
preHandle()

↓

Controller

↓

postHandle()

↓

View / Response

↓

afterCompletion()
```

Each stage allows custom logic to execute.

---

# preHandle()

Runs before the controller executes.

Example:

```java
@Override
public boolean preHandle(

        HttpServletRequest request,

        HttpServletResponse response,

        Object handler) {

    System.out.println("Before Controller");

    return true;

}
```

Returning:

```
true
```

allows the request to continue.

Returning:

```
false
```

stops request processing.

---

# postHandle()

Runs after the controller completes but before the response is sent.

Example:

```java
@Override
public void postHandle(

        HttpServletRequest request,

        HttpServletResponse response,

        Object handler,

        ModelAndView modelAndView) {

    System.out.println("After Controller");

}
```

---

# afterCompletion()

Runs after the entire request has completed.

Example:

```java
@Override
public void afterCompletion(

        HttpServletRequest request,

        HttpServletResponse response,

        Object handler,

        Exception ex) {

    System.out.println("Request Completed");

}
```

This method is commonly used for cleanup and final logging.

---

# Registering an Interceptor

Interceptors must be registered.

Example:

```java
@Configuration
public class WebConfig
        implements WebMvcConfigurer {

    @Override
    public void addInterceptors(
            InterceptorRegistry registry) {

        registry.addInterceptor(
                new LoggingInterceptor());

    }

}
```

Once registered, every request passes through the interceptor.

---

# Filter vs Interceptor

| Feature | Filter | Interceptor |
|----------|--------|-------------|
| Part of | Servlet API | Spring MVC |
| Executes Before DispatcherServlet | ✅ | ❌ |
| Executes Before Controller | ✅ | ✅ |
| Access to Controller Information | ❌ | ✅ |
| Can Modify Request/Response | ✅ | Limited |
| Framework Independent | ✅ | ❌ |
| Typical Use Cases | Encoding, CORS, Compression | Authentication, Logging, Auditing |

---

# Complete Request Lifecycle

```
Client

↓

Tomcat

↓

Filter

↓

DispatcherServlet

↓

Interceptor

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

Interceptor

↓

Filter

↓

Client
```

This is the request flow followed by most enterprise Spring Boot applications.

---

# Measuring Request Time

Interceptors are ideal for measuring execution time.

Example:

```
preHandle()

↓

Store Start Time

↓

Controller

↓

afterCompletion()

↓

Calculate Total Time

↓

Log Duration
```

This helps identify slow APIs.

---

# Authentication Example

A simple interceptor can verify authentication.

```
Request

↓

Authorization Header

↓

Valid?

↓

Yes → Controller

No → 401 Unauthorized
```

In production, this responsibility is usually handled by Spring Security.

---

# When to Use a Filter

Choose a Filter when you need to:

- Process every request
- Modify request or response objects
- Implement compression
- Configure character encoding
- Add security headers
- Perform low-level request processing

---

# When to Use an Interceptor

Choose an Interceptor when you need to:

- Authenticate users
- Check permissions
- Log controller execution
- Measure execution time
- Access handler information
- Perform MVC-specific processing

---

# Common Mistakes

❌ Using Filters for controller-specific logic.

❌ Performing database operations inside Filters.

❌ Forgetting to call `chain.doFilter()`.

❌ Returning `false` in `preHandle()` without sending an error response.

❌ Putting business logic inside Interceptors.

---

# Best Practices

- Keep Filters lightweight.
- Keep Interceptors focused on request processing.
- Avoid business logic in both.
- Use Filters for servlet-level concerns.
- Use Interceptors for Spring MVC concerns.
- Prefer Spring Security for authentication and authorization.

---

# Industry Insight

Enterprise applications often use **both** Filters and Interceptors together.

Typical flow:

```
Client

↓

Security Filter

↓

Logging Filter

↓

DispatcherServlet

↓

Authentication Interceptor

↓

Audit Interceptor

↓

Controller
```

Each component has a clearly defined responsibility, making the system easier to maintain and extend.

---

# Interview Corner

## Basic

1. What is a Filter?
2. What is an Interceptor?
3. What is the purpose of `preHandle()`?

---

## Intermediate

4. Explain the difference between Filters and Interceptors.
5. When would you use a Filter instead of an Interceptor?
6. What happens if `preHandle()` returns `false`?

---

## Advanced

7. Explain the complete request lifecycle involving Filters, Interceptors, and the DispatcherServlet.
8. How would you measure API execution time using an Interceptor?
9. Why is authentication typically handled by Spring Security instead of custom Interceptors?

---

# Hands-on Lab

## Objective

Add request logging to the Student Management System.

### Tasks

1. Create a `LoggingFilter`.
2. Log every incoming request URL.
3. Create a `LoggingInterceptor`.
4. Implement:
   - `preHandle()`
   - `postHandle()`
   - `afterCompletion()`
5. Register the interceptor using `WebMvcConfigurer`.
6. Call several REST endpoints and observe the execution order in the console.

---

# Cheat Sheet

- Filters belong to the Servlet API.
- Interceptors belong to Spring MVC.
- Filters execute before the `DispatcherServlet`.
- Interceptors execute after the `DispatcherServlet` but before the controller.
- `preHandle()` runs before controller execution.
- `postHandle()` runs after the controller but before the response.
- `afterCompletion()` runs after request completion.
- Use Filters for servlet-level concerns.
- Use Interceptors for Spring MVC concerns.

---

# Summary

Filters and Interceptors allow developers to implement cross-cutting concerns without polluting business logic. Filters operate at the servlet container level and are ideal for low-level request processing, while Interceptors integrate closely with Spring MVC and are better suited for authentication, auditing, and request monitoring. Understanding when and where each component executes is essential for designing clean, maintainable, and enterprise-grade Spring Boot applications.

---

# What's Next?

➡ **Chapter 13 – Cross-Origin Resource Sharing (CORS)**

In the next chapter, you'll learn why browsers block cross-origin requests, how CORS works, and how to configure Spring Boot applications to safely communicate with frontend applications running on different domains or ports.
