---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 6
title: Spring Beans - The Building Blocks of Every Spring Application
difficulty: Beginner
estimated_time: 120 Minutes
version: 1.0
---

# Chapter 6
# Spring Beans – The Building Blocks of Every Spring Application

> **"Every Spring Bean is a Java object, but not every Java object is a Spring Bean."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Define a Spring Bean.
- Differentiate between a Java Object and a Spring Bean.
- Understand how Spring creates and manages beans.
- Explain bean registration.
- Understand bean naming.
- Retrieve beans from the container.
- Explain Spring Beans confidently in interviews.

---

# 📚 Prerequisites

- Spring Core Architecture
- IoC
- IoC Container
- BeanFactory
- ApplicationContext

---

# 🗺 Module Progress

```
Spring Core

↓

IoC

↓

IoC Container

↓

BeanFactory

↓

ApplicationContext

↓

📍 Spring Beans

↓

Bean Lifecycle

↓

Dependency Injection
```

---

# ❓ Why Do We Need Beans?

Imagine a Student Management System.

```
StudentController

↓

StudentService

↓

StudentRepository
```

Should developers manually create every object?

```java
StudentRepository repository =
new StudentRepository();

StudentService service =
new StudentService(repository);

StudentController controller =
new StudentController(service);
```

This works.

But imagine

```
800 Classes
```

Creating every object manually quickly becomes difficult.

Spring solves this problem using **Beans**.

---

# 📖 What is a Spring Bean?

A Spring Bean is an object whose creation, configuration, dependency injection, and lifecycle are managed by the Spring IoC Container.

Instead of creating objects yourself using `new`, you let Spring create and manage them.

---

# Java Object vs Spring Bean

A Java object is any object created by the JVM.

Example:

```java
Student student = new Student();
```

This object is **not** managed by Spring.

Now look at a Spring-managed component:

```java
@Service
public class StudentService {

}
```

When the application starts, Spring creates an instance of `StudentService`, stores it inside the `ApplicationContext`, injects its dependencies, and manages its lifecycle.

This instance is a **Spring Bean**.

---

# Visual Comparison

```
Java Object

Developer

↓

new Student()

↓

Object Created

Done.
```

```
Spring Bean

@Component

↓

Component Scan

↓

Bean Definition

↓

ApplicationContext

↓

Bean Created

↓

Dependencies Injected

↓

Lifecycle Managed
```

---

# Real-Life Analogy

Imagine buying a car.

### Java Object

You buy the car.

You maintain it.

You insure it.

You repair it.

Everything is your responsibility.

---

### Spring Bean

You lease a company vehicle.

The company

- buys it
- insures it
- maintains it
- replaces it

You simply use it.

Spring manages beans the same way.

---

# Bean Creation Flow

```
Application Starts

↓

Component Scan

↓

Find @Component

↓

Create Bean Definition

↓

Instantiate Bean

↓

Inject Dependencies

↓

Store Bean

↓

Ready
```

---

# Example

StudentRepository

```java
@Repository
public class StudentRepository {

}
```

StudentService

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository){
        this.repository = repository;
    }

}
```

StudentController

```java
@RestController
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service){
        this.service = service;
    }

}
```

Notice that we never call:

```java
new StudentService();
```

The container creates it.

---

# 🧠 How Spring Thinks

Spring doesn't see isolated classes.

It sees a dependency graph.

```
StudentController

↓

StudentService

↓

StudentRepository
```

The container determines:

- what should be created,
- in which order,
- with which dependencies.

---

# Bean Naming

Every bean has a unique name inside the container.

Example

```java
@Service
public class StudentService {

}
```

Default bean name:

```
studentService
```

Spring converts the class name into camelCase.

Examples:

| Class | Bean Name |
|--------|-----------|
| StudentService | studentService |
| PaymentService | paymentService |
| EmailSender | emailSender |

You can also provide a custom name:

```java
@Service("studentServiceV1")
public class StudentService {

}
```

---

# Where Are Beans Stored?

Beans are stored inside the ApplicationContext.

```
ApplicationContext

├── studentController

├── studentService

├── studentRepository

├── objectMapper

├── dispatcherServlet

└── ...
```

When another component requires `StudentService`, the container supplies the managed instance instead of creating a new one.

---

# Bean Retrieval

Although constructor injection is the preferred approach, beans can also be retrieved manually.

```java
ApplicationContext context =
SpringApplication.run(Application.class, args);

StudentService service =
context.getBean(StudentService.class);
```

This asks the container to return the managed bean.

---

# Bean Identity

By default, Spring creates **one singleton instance** of each bean.

```
StudentController

↓

StudentService

↓

StudentRepository
```

Every request for `StudentService` receives the same instance unless a different scope is configured.

We'll study bean scopes later in this module.

---

# What Makes a Bean Different?

A Spring Bean has several capabilities that an ordinary Java object does not.

| Feature | Java Object | Spring Bean |
|---------|-------------|-------------|
| Managed by Container | ❌ | ✅ |
| Dependency Injection | ❌ | ✅ |
| Lifecycle Callbacks | ❌ | ✅ |
| Scope Management | ❌ | ✅ |
| AOP Support | ❌ | ✅ |
| Event Participation | ❌ | ✅ |

---

# Common Ways to Create Beans

Spring provides multiple ways to register beans.

## 1. Stereotype Annotations

```java
@Component
@Service
@Repository
@Controller
@RestController
```

Most common.

---

## 2. Java Configuration

```java
@Bean
public StudentService studentService() {
    return new StudentService();
}
```

Useful for third-party libraries or explicit configuration.

---

## 3. XML Configuration

```xml
<bean id="studentService"
      class="com.techvidyalaya.StudentService"/>
```

Common in legacy Spring applications but uncommon in modern Spring Boot projects.

---

# Behind the Scenes

When Spring detects

```java
@Service
```

it performs the following steps:

```
@Service

↓

Create Bean Definition

↓

Register Definition

↓

Instantiate Object

↓

Resolve Dependencies

↓

Inject Dependencies

↓

Run Initialization Callbacks

↓

Store Singleton Bean
```

---

# Industry Insight

In a typical Spring Boot application, many beans are not written by developers.

Spring Boot automatically registers beans for:

- Embedded Tomcat
- Jackson ObjectMapper
- DataSource
- DispatcherServlet
- Environment
- TaskExecutor
- MessageConverters

Your application often contains hundreds of framework-managed beans before you create your first controller.

---

# Common Mistakes

❌ Creating service classes with `new`.

This bypasses the Spring container.

As a result:

- dependencies won't be injected,
- lifecycle callbacks won't execute,
- AOP won't apply,
- transactions may not work.

Always allow Spring to create managed components.

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

Because Spring didn't create the object, injected dependencies remain `null`.

Solution:

Allow Spring to create the bean and inject it where needed.

---

# 🎤 Interview Corner

## Beginner

1. What is a Spring Bean?
2. Difference between a Java object and a Spring Bean?
3. Where are beans stored?

---

## Intermediate

4. How does Spring create beans?
5. How are bean names generated?
6. Name different ways to register beans.

---

## Advanced

7. Explain the complete lifecycle of a bean from component scanning to initialization.
8. Why shouldn't developers instantiate service classes manually?
9. How does ApplicationContext retrieve beans?

---

# 🧪 Hands-on Lab

## Objective

Create your first Spring-managed beans.

### Tasks

1. Create:

```
StudentRepository

StudentService

StudentController
```

2. Annotate them appropriately.

3. Start the application.

4. Print the total number of beans.

```java
System.out.println(
context.getBeanDefinitionCount()
);
```

5. Retrieve StudentService manually.

```java
StudentService service =
context.getBean(StudentService.class);
```

6. Verify that the retrieved bean is managed by Spring.

---

# 🎯 Mini Challenge

1. What is a Spring Bean?
2. How is it different from a Java object?
3. Where are beans stored?
4. How does Spring determine a default bean name?
5. Why is creating beans with `new` discouraged?

---

# 📌 Chapter Summary

A Spring Bean is a Java object managed by the Spring IoC Container. The container is responsible for creating the bean, injecting its dependencies, managing its lifecycle, and making it available throughout the application.

Understanding Spring Beans is fundamental because every service, controller, repository, and configuration class in a Spring application ultimately becomes a managed bean.

---

# 📋 Cheat Sheet

| Concept | Description |
|---------|-------------|
| Bean | Managed Java Object |
| Managed By | ApplicationContext |
| Default Scope | Singleton |
| Default Naming | camelCase of class name |
| Common Registration | @Component, @Service, @Repository, @Controller, @Bean |

---

# 🧠 Quiz

1. What is a Spring Bean?
2. Is every Java object a Spring Bean?
3. How are bean names generated?
4. Where are beans stored?
5. What are the advantages of Spring-managed beans?
6. What happens if you instantiate a service using `new`?
7. Name three ways to register a bean.

---

# 📖 What's Next?

➡ **Chapter 7 – Bean Lifecycle: From Creation to Destruction**

You'll explore exactly what happens to a bean after it's created, including initialization, lifecycle callbacks, destruction, and the sequence of events that occur inside the Spring container.
