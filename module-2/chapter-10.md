---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 10
title: Setter Injection & Field Injection - Choosing the Right Injection Style
difficulty: Intermediate
estimated_time: 180 Minutes
version: 1.0
---

# Chapter 10
# Setter Injection & Field Injection – Choosing the Right Injection Style

> **"There is no perfect injection style. There is only the right choice for the right dependency."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Setter Injection.
- Understand Field Injection.
- Compare all three injection styles.
- Identify required vs optional dependencies.
- Understand why constructor injection is usually preferred.
- Recognize common code smells.

---

# 📚 Prerequisites

- Dependency Injection
- Constructor Injection
- Bean Lifecycle

---

# 🗺 Module Progress

```
Dependency Injection

↓

Constructor Injection

↓

📍 Setter Injection

↓

📍 Field Injection

↓

Component Scanning
```

---

# ❓ Why Does Spring Support Three Injection Styles?

Different applications have different requirements.

Some dependencies are:

- Required
- Optional
- Configurable
- Replaceable

Spring therefore provides multiple ways to inject dependencies.

---

# 1. Constructor Injection

Review:

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository){
        this.repository = repository;
    }

}
```

Characteristics:

- Required dependency
- Immutable
- Recommended
- Easy to test

---

# 2. Setter Injection

Instead of providing dependencies through the constructor,

Spring calls a setter method after the object is created.

```java
@Service
public class StudentService {

    private NotificationService notificationService;

    @Autowired
    public void setNotificationService(
            NotificationService notificationService){

        this.notificationService =
                notificationService;

    }

}
```

---

# Internal Flow

```
Constructor

↓

Object Created

↓

Setter Method

↓

Dependency Injected

↓

Bean Ready
```

Unlike constructor injection,

the object exists before every dependency has been assigned.

---

# When Should Setter Injection Be Used?

Setter injection is suitable for:

- Optional dependencies
- Feature toggles
- Configurable collaborators
- Legacy systems
- Dependencies that may legitimately be absent

Example:

```java
Optional<NotificationService>
```

A reporting service may function even when email notifications are disabled.

---

# Advantages

✔ Optional dependencies

✔ Flexible configuration

✔ Easy to replace after creation

✔ Useful in legacy applications

---

# Disadvantages

❌ Mutable object

❌ Object may be incomplete

❌ Harder to guarantee validity

---

# 3. Field Injection

Example

```java
@Service
public class StudentService {

    @Autowired
    private StudentRepository repository;

}
```

No constructor.

No setter.

Spring injects directly into the field using reflection.

---

# Inside Spring

```
Create Object

↓

Reflection

↓

Locate Field

↓

Inject Dependency

↓

Bean Ready
```

---

# Why Is Field Injection Popular?

Because it is short.

```
@Autowired

private Repository repository;
```

No constructor.

No setter.

Many beginners prefer it because it reduces boilerplate.

---

# Why Is It Discouraged?

Field injection hides the class's dependencies.

Looking only at the constructor:

```java
public StudentService(){

}
```

you cannot tell what the class needs to function.

Dependencies become implicit rather than explicit.

---

# Testing Problem

Suppose we want to unit test.

Constructor Injection:

```java
StudentService service =
new StudentService(fakeRepository);
```

Easy.

Field Injection:

```java
StudentService service =
new StudentService();
```

The repository field is never injected because Spring is not involved.

You must use reflection or a mocking framework to populate the field, making plain unit tests more cumbersome.

---

# Design Review ⭐

Consider this constructor.

```java
public StudentService(

StudentRepository repository,

NotificationService notification,

AuditService audit,

PaymentService payment,

CacheService cache,

EmailService email,

PdfService pdf,

ExcelService excel,

AnalyticsService analytics

)
```

Question:

What is the real problem?

It is **not** constructor injection.

The real issue is that the class has too many responsibilities.

This is a sign of a possible **Single Responsibility Principle (SRP)** violation.

Refactor instead of blaming constructor injection.

---

# Comparison Table

| Feature | Constructor | Setter | Field |
|----------|-------------|---------|--------|
| Required Dependencies | ✅ | ❌ | ❌ |
| Optional Dependencies | ⚠ Possible but uncommon | ✅ | ⚠ Possible |
| Immutable | ✅ | ❌ | ❌ |
| Testability | ⭐ Excellent | Good | Limited |
| Visibility of Dependencies | Excellent | Good | Poor |
| Recommended for New Projects | ✅ | Limited use | Rarely |

---

# Required vs Optional Dependencies

Required:

```
StudentRepository

↓

StudentService
```

Without the repository,

StudentService cannot work.

Use:

Constructor Injection.

---

Optional:

```
NotificationService

↓

StudentService
```

StudentService can still save student records even if notifications are disabled.

Setter injection is a reasonable choice here.

---

# How Spring Thinks

```
Constructor

↓

Mandatory

↓

Must Exist

↓

Create Bean
```

```
Setter

↓

Optional

↓

Can Be Assigned Later
```

```
Field

↓

Inject Directly

↓

Developer Convenience
```

---

# Performance

The runtime performance differences among constructor, setter, and field injection are negligible in most applications.

Choose an injection style based on:

- Correctness
- Readability
- Maintainability
- Testability

—not micro-optimizations.

---

# Common Mistakes

❌ Using field injection for every dependency simply because it is shorter.

❌ Using setter injection for mandatory dependencies.

❌ Assuming constructor injection causes long constructors.

The long constructor is often revealing a design issue, not a framework issue.

---

# 🐞 Debugging Corner

### Problem

```text
NullPointerException
```

Cause:

```java
StudentService service =
new StudentService();
```

With field injection, dependencies are only injected when Spring creates the object.

---

### Problem

```text
BeanCreationException
```

Cause:

A required constructor dependency cannot be resolved.

---

# 🏢 Industry Insight

Modern Spring Boot teams typically adopt these guidelines:

- Constructor injection for required collaborators.
- Setter injection only for genuine optional dependencies.
- Avoid field injection in production business code because it hides dependencies and complicates testing.

Some existing codebases still use field injection extensively, so you'll encounter it in legacy applications.

---

# 🎤 Interview Corner

## Beginner

1. What are the three types of Dependency Injection?
2. Which one does Spring recommend?

---

## Intermediate

3. Why is constructor injection preferred?
4. When is setter injection appropriate?
5. Why is field injection often discouraged?

---

## Advanced

6. Explain how Spring performs field injection internally.
7. Does field injection violate encapsulation?
8. Why does constructor injection improve unit testing?

---

# 🧪 Hands-on Lab

## Objective

Implement the same service using all three injection styles.

### Step 1

Create:

```
StudentRepository

StudentService

NotificationService
```

### Step 2

Implement:

- Constructor Injection
- Setter Injection
- Field Injection

### Step 3

Write unit tests.

Compare:

- Readability
- Ease of testing
- Dependency visibility

Record your observations.

---

# 🎯 Mini Challenge

1. Which injection style should be used for required dependencies?
2. Which style is appropriate for optional dependencies?
3. Why is field injection harder to unit test?
4. What does a constructor with 12 parameters suggest?
5. Which style would you recommend for a new Spring Boot project and why?

---

# 📌 Chapter Summary

Spring supports constructor, setter, and field injection because each addresses different design needs. Constructor injection is generally the best choice for required dependencies because it enforces object validity, supports immutability, and simplifies testing. Setter injection is useful for optional collaborators, while field injection offers convenience but hides dependencies and is less suitable for maintainable business code.

---

# 📋 Cheat Sheet

| Injection Style | Best Use Case |
|-----------------|---------------|
| Constructor | Required dependencies |
| Setter | Optional dependencies |
| Field | Legacy code or framework-managed convenience |

---

# 🧠 Quiz

1. What are the three dependency injection styles?
2. Why is constructor injection recommended?
3. When should setter injection be used?
4. Why is field injection difficult to unit test?
5. What design principle is often violated when constructors become excessively large?

---

# 📖 What's Next?

➡ **Chapter 11 – Component Scanning: How Spring Automatically Discovers Your Beans**

You'll learn how Spring finds `@Component`, `@Service`, `@Repository`, and `@Controller` classes, how package scanning works, how to customize scanning with `@ComponentScan`, and how bean definitions are automatically registered during application startup.
