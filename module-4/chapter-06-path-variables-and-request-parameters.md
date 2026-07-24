---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 6
title: Path Variables and Request Parameters
difficulty: Beginner
estimated_reading_time: 55 Minutes
estimated_coding_time: 40 Minutes
estimated_lab_time: 30 Minutes
version: 1.0
---

# Chapter 6
# Path Variables and Request Parameters

> **"A great API doesn't just receive requests—it understands exactly what the client is asking for."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand dynamic URLs in REST APIs.
- Learn how to use `@PathVariable`.
- Learn how to use `@RequestParam`.
- Understand the differences between path variables and query parameters.
- Handle optional and default request parameters.
- Design clean and RESTful URLs.

---

# Introduction

Consider the following URLs:

```
GET /students/101
```

```
GET /students?department=CSE
```

```
GET /students?page=1&size=20
```

All three requests are different.

The first requests a **specific student**.

The second filters students by **department**.

The third requests **paginated results**.

How does Spring extract these values?

The answer lies in two important annotations:

- `@PathVariable`
- `@RequestParam`

These annotations allow Spring MVC to bind values from the URL directly to Java method parameters.

---

# Understanding Dynamic URLs

Not every URL is fixed.

Consider these examples:

```
/students/101

/students/205

/students/999
```

The structure remains the same.

Only the student ID changes.

Instead of creating hundreds of mappings, Spring allows us to capture dynamic values.

---

# What is a Path Variable?

A **Path Variable** is a value embedded directly in the URL path.

Example:

```
GET /students/101
```

Here,

```
101
```

is the path variable.

---

# Using @PathVariable

Spring automatically extracts values enclosed in `{}`.

Example:

```java
@GetMapping("/{id}")
public Student getStudent(
        @PathVariable Long id) {

    return studentService.findById(id);

}
```

Request:

```
GET /students/101
```

Value received:

```
id = 101
```

---

# Request Flow

```
GET /students/101

↓

DispatcherServlet

↓

StudentController

↓

@PathVariable

↓

id = 101

↓

StudentService
```

Spring performs this binding automatically.

---

# Multiple Path Variables

A URL may contain multiple dynamic values.

Example:

```
GET /students/101/courses/5
```

Controller:

```java
@GetMapping("/{studentId}/courses/{courseId}")
public String getCourse(
        @PathVariable Long studentId,
        @PathVariable Long courseId) {

    return "Student "
            + studentId
            + " Course "
            + courseId;

}
```

Request:

```
GET /students/101/courses/5
```

Output:

```
Student 101 Course 5
```

---

# Renaming Path Variables

Sometimes the URL variable name differs from the Java parameter.

Example:

```java
@GetMapping("/{id}")
public Student getStudent(
        @PathVariable("id") Long studentId) {

    return studentService.findById(studentId);

}
```

Spring maps:

```
{id}

↓

studentId
```

---

# What is a Request Parameter?

A **Request Parameter** (also called a Query Parameter) appears after the `?` symbol in the URL.

Example:

```
GET /students?department=CSE
```

Here,

```
department=CSE
```

is a request parameter.

---

# Using @RequestParam

Example:

```java
@GetMapping
public List<Student> getStudents(
        @RequestParam String department) {

    return studentService.findByDepartment(department);

}
```

Request:

```
GET /students?department=CSE
```

Value:

```
department = CSE
```

---

# Multiple Request Parameters

Example:

```
GET /students?page=1&size=20
```

Controller:

```java
@GetMapping
public List<Student> getStudents(

        @RequestParam int page,

        @RequestParam int size) {

    return studentService.findAll(page, size);

}
```

Spring automatically binds both values.

---

# Optional Request Parameters

Some parameters may not always be supplied.

Example:

```java
@GetMapping
public List<Student> getStudents(

        @RequestParam(required = false)
        String department) {

    return studentService.findAll();

}
```

Now both requests are valid:

```
GET /students
```

```
GET /students?department=CSE
```

---

# Default Values

Spring can provide default values.

Example:

```java
@GetMapping
public List<Student> getStudents(

    @RequestParam(defaultValue = "1")
    int page,

    @RequestParam(defaultValue = "10")
    int size) {

    return studentService.findAll(page, size);

}
```

Request:

```
GET /students
```

Values:

```
page = 1

size = 10
```

---

# Path Variable vs Request Parameter

| Feature | Path Variable | Request Parameter |
|----------|---------------|------------------|
| Location | URL Path | Query String |
| Used For | Identifying a resource | Filtering, searching, pagination, sorting |
| Required | Usually Yes | Often Optional |
| Example | `/students/101` | `/students?page=1` |

---

# When to Use Path Variables

Use path variables when identifying a specific resource.

Examples:

```
GET /students/101

GET /courses/5

DELETE /teachers/9

PUT /students/101
```

The resource identity is part of the URL.

---

# When to Use Request Parameters

Use request parameters for:

- Searching
- Filtering
- Sorting
- Pagination

Examples:

```
GET /students?page=1

GET /students?size=20

GET /students?department=CSE

GET /students?sort=name
```

These modify how resources are retrieved rather than identifying a specific resource.

---

# Combining Both

Both annotations can be used together.

Example:

```
GET /students/101/results?semester=6
```

Controller:

```java
@GetMapping("/{id}/results")
public Result getResult(

        @PathVariable Long id,

        @RequestParam int semester) {

    return resultService.find(id, semester);

}
```

Spring extracts:

```
id = 101

semester = 6
```

---

# Complete Example

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping("/{id}")
    public Student getStudent(
            @PathVariable Long id) {

        return studentService.findById(id);
    }

    @GetMapping
    public List<Student> searchStudents(

            @RequestParam(required = false)
            String department,

            @RequestParam(defaultValue = "1")
            int page,

            @RequestParam(defaultValue = "10")
            int size) {

        return studentService.search(
                department,
                page,
                size);

    }

}
```

This demonstrates both resource retrieval and flexible searching in a single controller.

---

# Common Mistakes

❌ Using request parameters to identify resources.

```
/student?id=101
```

Preferred:

```
/students/101
```

---

❌ Using path variables for filtering.

```
/students/CSE
```

Better:

```
/students?department=CSE
```

---

❌ Forgetting to mark optional parameters as `required = false`.

---

❌ Hardcoding values instead of using dynamic URLs.

---

# Best Practices

- Use path variables for resource identifiers.
- Use request parameters for filters and search criteria.
- Keep URLs meaningful and RESTful.
- Use default values for pagination.
- Validate incoming parameters before processing.

---

# Industry Insight

Most enterprise APIs follow predictable URL conventions.

Examples:

```
GET /users/15

GET /orders/1001

GET /products?page=2

GET /employees?department=HR

GET /transactions?from=2026-01-01&to=2026-01-31
```

Following these conventions makes APIs easier for frontend developers and third-party clients to understand.

---

# Interview Corner

## Basic

1. What is a path variable?
2. What is a request parameter?
3. Which annotation reads query parameters?

---

## Intermediate

4. Explain the difference between `@PathVariable` and `@RequestParam`.
5. How do you make a request parameter optional?
6. How can you specify a default value for a request parameter?

---

## Advanced

7. Design REST endpoints for searching books by author and category.
8. When would you combine path variables and request parameters?
9. Why are path variables preferred for identifying resources?

---

# Hands-on Lab

## Objective

Extend the Student Management System with dynamic URLs.

### Tasks

1. Create an endpoint:

```text
GET /students/{id}
```

2. Create another endpoint:

```text
GET /students?department=CSE
```

3. Add pagination:

```text
GET /students?page=1&size=20
```

4. Add sorting:

```text
GET /students?sort=name
```

5. Test each endpoint using Postman or IntelliJ HTTP Client.

---

# Cheat Sheet

- `@PathVariable` extracts values from the URL path.
- `@RequestParam` extracts query parameters.
- Use path variables to identify resources.
- Use request parameters for filtering and pagination.
- `required = false` makes parameters optional.
- `defaultValue` supplies a fallback value.

---

# Summary

Path variables and request parameters allow Spring MVC applications to handle dynamic client requests in a clean and RESTful manner. Path variables uniquely identify resources, while request parameters customize how those resources are retrieved through filtering, searching, sorting, or pagination. Mastering these annotations is essential for building flexible, intuitive, and industry-standard REST APIs.

---

# What's Next?

➡ **Chapter 7 – Request Body and Response Body**

In the next chapter, you'll learn how Spring MVC maps JSON request payloads to Java objects using `@RequestBody`, serializes Java objects into JSON with `@ResponseBody`, and powers the data exchange used by modern RESTful web services.
