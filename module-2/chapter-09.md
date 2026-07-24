---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 9
title: Constructor Injection - The Recommended Way to Wire Dependencies
difficulty: Intermediate
estimated_time: 210 Minutes
version: 1.0
---

# Chapter 9
# Constructor Injection – The Recommended Way

> **"Constructor Injection isn't just Spring's recommendation—it's one of the best object-oriented design practices."**

---

# 🎯 Learning Objectives

After completing this chapter you will be able to:

- Understand Constructor Injection.
- Explain why Spring recommends it.
- Understand constructor resolution.
- Handle multiple constructors.
- Understand immutability.
- Write highly testable code.
- Debug dependency injection problems.

---

# 📚 Prerequisites

- Dependency Injection
- Spring Beans
- ApplicationContext

---

# 🗺 Module Progress

```
Spring Core

↓

Beans

↓

Dependency Injection

↓

📍 Constructor Injection

↓

Setter Injection

↓

Field Injection
```

---

# ❓ Why Constructor Injection?

Imagine a StudentService.

It **cannot work** without StudentRepository.

The dependency is mandatory.

Should we allow this?

```java
StudentService service =
new StudentService();
```

No.

The object is incomplete.

Constructor Injection prevents creating invalid objects.

---

# Traditional Programming

```java
StudentService service =
new StudentService();

service.setRepository(
new StudentRepository()
);
```

The object exists before it is fully initialized.

This creates the possibility of bugs.

---

# Constructor Injection

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository){
        this.repository = repository;
    }

}
```

The object **cannot exist** without its required dependency.

---

# Why Spring Recommends Constructor Injection

Constructor Injection provides:

- Immutable objects
- Mandatory dependencies
- Easier testing
- Better readability
- Thread safety
- Explicit design

For these reasons, it is the preferred approach in modern Spring applications.

---

# Final Fields

```java
private final StudentRepository repository;
```

`final` ensures the dependency can be assigned only once.

Benefits:

- Prevents accidental reassignment.
- Makes object state predictable.
- Encourages immutable design.

---

# Inside Spring 🧠

When Spring encounters:

```java
public StudentService(StudentRepository repository)
```

it performs the following sequence:

```
Find Bean Definition

↓

Locate Constructor

↓

Inspect Parameters

↓

StudentRepository Required

↓

Search Container

↓

Bean Found

↓

Invoke Constructor

↓

Store Bean

↓

Ready
```

Spring uses reflection to inspect the constructor and resolve each parameter from the container.

---

# Constructor Resolution

Consider this class:

```java
@Service
public class StudentService {

    public StudentService() {}

    public StudentService(StudentRepository repository) {}

}
```

Which constructor should Spring choose?

Modern Spring prefers:

- A single constructor automatically.
- If multiple constructors exist, annotate the desired one with `@Autowired`.

Example:

```java
@Autowired
public StudentService(StudentRepository repository){
    this.repository = repository;
}
```

This removes ambiguity.

---

# Optional Dependencies

Sometimes a dependency is optional.

Spring allows:

```java
@Autowired(required = false)
```

or

```java
Optional<NotificationService>
```

Use optional dependencies sparingly and only when the business requirement truly allows the component to operate without them.

---

# Unit Testing Without Spring ⭐

One of the biggest advantages of constructor injection is simple unit testing.

```java
StudentRepository mockRepository =
new FakeStudentRepository();

StudentService service =
new StudentService(mockRepository);
```

No Spring container is required.

No ApplicationContext.

Just plain Java.

This makes tests fast and focused.

---

# Multiple Implementations

Suppose you have:

```java
EmailNotificationService

SmsNotificationService
```

Both implement:

```java
NotificationService
```

Spring now sees two beans.

Which one should it inject?

Without additional guidance, startup fails because the dependency is ambiguous.

---

# @Primary

```java
@Primary
@Service
public class EmailNotificationService
```

The container selects this bean by default whenever a `NotificationService` is requested.

---

# @Qualifier

```java
public StudentService(
    @Qualifier("smsNotificationService")
    NotificationService notificationService
)
```

`@Qualifier` tells Spring exactly which bean to inject.

---

# Circular Dependency

Imagine:

```
StudentService

↓

NotificationService

↓

StudentService
```

Both constructors require each other.

Spring cannot decide which bean to create first.

Typical error:

```
BeanCurrentlyInCreationException
```

The best solution is to redesign the dependency graph rather than relying on workarounds.

---

# How Spring Thinks

```
StudentService

needs

StudentRepository

↓

Container

↓

Do I already have StudentRepository?

↓

Yes

↓

Pass it to Constructor
```

The container builds the object graph one dependency at a time.

---

# Performance

Constructor injection is efficient because:

- Dependencies are resolved once during bean creation.
- `final` references reduce accidental mutation.
- Objects are fully initialized before use.

The performance difference between constructor and setter injection is usually negligible. The primary reasons to choose constructor injection are **design, correctness, and maintainability**, not raw speed.

---

# Common Mistakes

❌ Field Injection with required dependencies.

❌ Constructors containing business logic.

❌ Injecting too many dependencies.

If a constructor has 10–15 parameters, it often indicates that the class has too many responsibilities and should be refactored.

---

# 🐞 Debugging Corner

### Error

```
NoUniqueBeanDefinitionException
```

Cause:

```
EmailNotificationService

↓

SmsNotificationService
```

Both satisfy the same interface.

Fix:

- Use `@Primary`.
- Or specify `@Qualifier`.

---

### Error

```
BeanCurrentlyInCreationException
```

Cause:

Circular constructor dependency.

Solution:

Redesign responsibilities to remove the cycle.

---

# 🏢 Industry Insight

Most enterprise Spring projects adopt constructor injection as the default standard. It works well with immutable objects, simplifies unit testing, and makes dependencies visible in the class design.

Many organizations also use tools such as Lombok's `@RequiredArgsConstructor` to reduce boilerplate while preserving constructor injection.

---

# 🎤 Interview Corner

### Beginner

1. What is Constructor Injection?
2. Why is it preferred?
3. What is a required dependency?

---

### Intermediate

4. Constructor vs Setter Injection?
5. Why use `final` fields?
6. How does Spring resolve constructor parameters?

---

### Advanced

7. Explain constructor resolution.
8. What causes a circular dependency?
9. How do `@Primary` and `@Qualifier` work together?

---

# 🧪 Hands-on Lab

## Objective

Convert the Student Management System to constructor injection.

### Tasks

1. Create:

- StudentRepository
- StudentService
- StudentController

2. Use constructor injection only.

3. Mark injected fields as `final`.

4. Create two `NotificationService` implementations.

5. Observe the startup failure.

6. Fix it using:

- `@Primary`
- then `@Qualifier`

7. Write a unit test by manually constructing `StudentService` with a fake repository implementation.

---

# 🎯 Mini Challenge

1. Why does Spring recommend constructor injection?
2. Why are `final` fields valuable?
3. How does Spring choose a constructor?
4. What causes `NoUniqueBeanDefinitionException`?
5. What causes `BeanCurrentlyInCreationException`?
6. Why is constructor injection easier to test?

---

# 📌 Chapter Summary

Constructor injection is the recommended dependency injection style in Spring because it guarantees that required dependencies are available when an object is created. It promotes immutability, improves testability, makes class design explicit, and aligns with object-oriented design principles.

Understanding constructor injection also helps explain how Spring resolves dependencies, handles multiple implementations, and detects circular dependencies during application startup.

---

# 📋 Cheat Sheet

| Topic | Key Point |
|--------|-----------|
| Constructor Injection | Preferred DI style |
| Required Dependencies | Constructor parameters |
| Immutability | Use `final` fields |
| Multiple Beans | `@Primary` / `@Qualifier` |
| Circular Dependency | Redesign to remove the cycle |

---

# 📖 What's Next?

➡ **Chapter 10 – Setter Injection & Field Injection: When (and When Not) to Use Them**

You'll compare setter, field, and constructor injection, learn their trade-offs, and understand why constructor injection is the default choice for required dependencies while setter injection is better suited for optional ones.
