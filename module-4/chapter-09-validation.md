---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 9
title: Bean Validation
difficulty: Intermediate
estimated_reading_time: 70 Minutes
estimated_coding_time: 50 Minutes
estimated_lab_time: 35 Minutes
version: 1.0
---

# Chapter 9
# Bean Validation

> **"Never trust user input. A professional application validates every request before processing it."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand why input validation is essential.
- Learn the Bean Validation specification.
- Use common validation annotations.
- Validate incoming request bodies using `@Valid`.
- Create custom validation messages.
- Handle validation failures effectively.
- Follow enterprise validation best practices.

---

# Introduction

Suppose a client sends the following request to create a new student.

```http
POST /students

Content-Type: application/json

{
    "name": "",
    "email": "invalid-email",
    "department": null
}
```

Without validation, this data could be stored in the database.

The result?

- Empty names
- Invalid email addresses
- Missing departments
- Corrupted business data

Once incorrect data enters the system, fixing it becomes difficult and expensive.

This is why **validation should happen before business logic executes.**

---

# What is Bean Validation?

Bean Validation is the Java standard for validating objects.

Spring Boot integrates with the **Jakarta Bean Validation** specification and automatically validates incoming request objects.

Validation rules are declared using annotations.

Example:

```java
@NotBlank
private String name;
```

Spring checks these rules automatically before the controller processes the request.

---

# Why Validation Matters

Validation improves:

- Data quality
- Application reliability
- Security
- User experience

Without validation:

```
Client

↓

Invalid Data

↓

Database

↓

Future Problems
```

With validation:

```
Client

↓

Validation

↓

Valid Data

↓

Business Logic

↓

Database
```

---

# Adding Validation Support

Spring Boot validation is provided through the validation starter.

For Maven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Once added, validation annotations become available throughout the application.

---

# Common Validation Annotations

| Annotation | Purpose |
|------------|---------|
| `@NotNull` | Value must not be `null` |
| `@NotBlank` | String cannot be blank |
| `@NotEmpty` | Collection or String cannot be empty |
| `@Size` | Restricts length or collection size |
| `@Min` | Minimum numeric value |
| `@Max` | Maximum numeric value |
| `@Positive` | Value must be greater than zero |
| `@Negative` | Value must be less than zero |
| `@Email` | Valid email format |
| `@Pattern` | Matches a regular expression |
| `@Past` | Date must be in the past |
| `@Future` | Date must be in the future |

---

# Creating a Request DTO

Instead of validating entities directly, enterprise applications validate **DTOs**.

Example:

```java
public class StudentRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @NotBlank
    private String department;

}
```

This keeps validation rules separate from persistence logic.

---

# Understanding @Valid

The `@Valid` annotation tells Spring to validate the object before executing the controller method.

Example:

```java
@PostMapping
public Student createStudent(

        @Valid
        @RequestBody StudentRequest request) {

    return studentService.save(request);

}
```

Flow:

```
JSON Request

↓

@RequestBody

↓

StudentRequest

↓

@Valid

↓

Validation

↓

Controller
```

If validation fails, the controller method is not executed.

---

# Example Validation

DTO:

```java
public class StudentRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

}
```

Request:

```json
{
    "name": "",
    "email": "abc"
}
```

Spring automatically detects:

- Name is blank
- Email format is invalid

The request is rejected before reaching the service layer.

---

# Custom Validation Messages

Default messages are useful, but custom messages improve API usability.

Example:

```java
@NotBlank(message = "Student name is required.")

@Email(message = "Please enter a valid email address.")
private String email;
```

Now the client receives meaningful error messages.

---

# Using @Size

Example:

```java
@Size(min = 3,
      max = 50,
      message = "Name must contain between 3 and 50 characters.")
private String name;
```

Valid:

```
Rahul
```

Invalid:

```
A
```

---

# Numeric Validation

Example:

```java
@Min(18)

@Max(60)
private Integer age;
```

Valid values:

```
18

25

45

60
```

Invalid:

```
16

75
```

---

# Pattern Validation

Regular expressions can enforce specific formats.

Example:

```java
@Pattern(
    regexp = "^[A-Z]{2}[0-9]{4}$",
    message = "Course code must follow the format CS1001."
)
private String courseCode;
```

Valid:

```
CS1001
```

Invalid:

```
ComputerScience
```

---

# Nested Validation

Objects can contain other validated objects.

Example:

```java
public class StudentRequest {

    @Valid
    private AddressRequest address;

}
```

Spring recursively validates nested objects.

---

# Validation Flow

```
Client

↓

JSON Request

↓

@RequestBody

↓

StudentRequest

↓

@Valid

↓

Validation Engine

↓

Valid?

↓

Yes

↓

Controller

↓

Service

↓

Database
```

If validation fails:

```
Validation Failed

↓

400 Bad Request
```

---

# Default Validation Response

Example response:

```json
{
    "timestamp": "...",
    "status": 400,
    "error": "Bad Request",
    "path": "/students"
}
```

In the next chapter, we'll customize this response to provide detailed validation errors.

---

# Validation vs Business Rules

Validation checks whether incoming data is structurally correct.

Examples:

- Name cannot be blank.
- Email format must be valid.
- Age must be positive.

Business rules are different.

Examples:

- Email must be unique.
- Student cannot enroll twice.
- Maximum enrollment is five courses.

Validation occurs before business logic.

---

# DTO Validation vs Entity Validation

Recommended architecture:

```
Client

↓

StudentRequest DTO

↓

Validation

↓

Service

↓

Student Entity

↓

Database
```

This approach keeps API validation independent of database design.

---

# Common Mistakes

❌ Forgetting to use `@Valid`.

❌ Validating entities instead of DTOs.

❌ Relying only on frontend validation.

❌ Writing validation logic manually inside controllers.

❌ Returning unclear validation messages.

---

# Best Practices

- Validate every incoming request.
- Use DTOs instead of entities.
- Keep validation rules close to the DTO.
- Write user-friendly validation messages.
- Separate validation rules from business rules.
- Validate both client-side and server-side.

---

# Industry Insight

Large enterprise systems process millions of API requests every day.

Even if frontend applications validate user input, backend validation is still mandatory because requests may originate from:

- Mobile applications
- Third-party integrations
- Automated scripts
- Internal services
- Public APIs

Never assume incoming data is trustworthy.

---

# Interview Corner

## Basic

1. What is Bean Validation?
2. What does `@Valid` do?
3. What is the purpose of `@NotBlank`?

---

## Intermediate

4. Explain the difference between `@NotNull`, `@NotEmpty`, and `@NotBlank`.
5. Why should validation happen before business logic?
6. Why are DTOs preferred for validation?

---

## Advanced

7. Explain the complete validation lifecycle in Spring MVC.
8. How would you validate nested request objects?
9. What is the difference between validation rules and business rules?

---

# Hands-on Lab

## Objective

Validate requests for the Student Management System.

### Tasks

1. Create a `StudentRequest` DTO.
2. Add:
   - `@NotBlank`
   - `@Email`
   - `@Size`
   - `@Min`
3. Use `@Valid` in the controller.
4. Send valid requests.
5. Send invalid requests.
6. Observe the generated `400 Bad Request` responses.
7. Add custom validation messages and test again.

---

# Cheat Sheet

- Bean Validation validates Java objects.
- `@Valid` enables validation in controller methods.
- Use DTOs for request validation.
- `@NotBlank` → Non-empty text.
- `@Email` → Valid email address.
- `@Size` → Length constraints.
- `@Min` / `@Max` → Numeric constraints.
- Validation occurs before business logic.
- Invalid requests automatically return **400 Bad Request**.

---

# Summary

Bean Validation ensures that only well-formed and meaningful data enters your application. By using annotations such as `@NotBlank`, `@Email`, `@Size`, and `@Valid`, Spring Boot automatically validates incoming request objects before they reach the service layer. Combined with DTOs and clear validation messages, this approach improves data quality, strengthens security, and makes APIs more reliable and user-friendly.

---

# What's Next?

➡ **Chapter 10 – Global Exception Handling**

In the next chapter, you'll learn how to handle exceptions consistently across your application using `@ControllerAdvice` and `@ExceptionHandler`, including customizing validation error responses to create professional, developer-friendly REST APIs.
