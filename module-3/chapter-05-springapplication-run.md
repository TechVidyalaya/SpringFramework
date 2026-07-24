---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 5
title: Inside SpringApplication.run() – Understanding the Spring Boot Startup Process
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_coding_time: 30 Minutes
estimated_lab_time: 20 Minutes
version: 1.0
---

# Chapter 5
# Inside `SpringApplication.run()` – Understanding the Spring Boot Startup Process

> **"A Spring Boot application starts with one line of code, but behind that line a complete application is prepared, configured, and launched."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Explain the purpose of `SpringApplication.run()`
- Understand the Spring Boot startup lifecycle
- Identify the major startup phases
- Understand where beans are created
- Know when the embedded server starts
- Debug startup failures more effectively

---

# Introduction

Every Spring Boot application starts with the same code.

```java
@SpringBootApplication
public class StudentApplication {

    public static void main(String[] args) {
        SpringApplication.run(StudentApplication.class, args);
    }

}
```

Most developers write this code every day.

Very few understand what actually happens after calling `SpringApplication.run()`.

This chapter explains the complete startup journey.

---

# What is SpringApplication.run()?

`SpringApplication.run()` is responsible for starting an entire Spring Boot application.

It performs tasks such as:

- Preparing the application environment
- Creating the ApplicationContext
- Loading bean definitions
- Applying auto configuration
- Creating Spring beans
- Starting the embedded server
- Making the application ready to accept requests

Think of it as the **startup engine** of Spring Boot.

---

# High-Level Startup Flow

```
main()

↓

SpringApplication.run()

↓

Prepare Environment

↓

Create ApplicationContext

↓

Load Bean Definitions

↓

Create Beans

↓

Dependency Injection

↓

Start Embedded Server

↓

Application Ready
```

We'll briefly understand each step.

---

# Step 1: Prepare the Environment

Spring first creates the application environment.

It loads configuration from different sources.

Examples:

- application.properties
- application.yml
- Environment Variables
- JVM Properties
- Command Line Arguments

These values are stored inside the **Environment** object.

---

# Step 2: Create ApplicationContext

Next, Spring Boot creates the IoC Container.

```
ApplicationContext
```

This container is responsible for:

- Managing beans
- Dependency Injection
- Bean lifecycle
- Event publishing

Without the ApplicationContext, Spring cannot function.

---

# Step 3: Load Bean Definitions

Spring scans your project.

It looks for annotations like:

```
@Component

@Service

@Repository

@Controller

@Configuration
```

Instead of creating objects immediately, Spring first creates **Bean Definitions**.

A Bean Definition is simply a blueprint describing how a bean should be created.

---

# Step 4: Create Beans

Using the Bean Definitions, Spring creates the actual objects.

Example:

```
StudentController

↓

StudentService

↓

StudentRepository
```

These objects are called **Spring Beans**.

---

# Step 5: Dependency Injection

Once beans are created, Spring injects dependencies.

Example:

```java
@RestController
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

}
```

Spring automatically provides the required `StudentService`.

---

# Step 6: Auto Configuration

Spring Boot checks the available dependencies.

For example,

If the project contains:

```
spring-boot-starter-web
```

Spring automatically configures:

- DispatcherServlet
- Tomcat
- Jackson
- Error Handling

without additional configuration.

We'll study this deeply in the next chapter.

---

# Step 7: Start Embedded Server

If the project is a web application,

Spring Boot starts an embedded web server.

Usually,

```
Tomcat
```

The server binds to port **8080** by default.

At this point,

your application can accept HTTP requests.

---

# Step 8: Application Ready

Finally,

Spring executes startup callbacks like:

- ApplicationRunner
- CommandLineRunner

After that,

you'll see:

```
Started StudentApplication in 2.5 seconds
```

The application is now fully running.

---

# Complete Startup Lifecycle

```
main()

↓

SpringApplication.run()

↓

Prepare Environment

↓

Create ApplicationContext

↓

Component Scan

↓

Load Bean Definitions

↓

Create Beans

↓

Dependency Injection

↓

Auto Configuration

↓

Start Tomcat

↓

Application Ready
```

Remember this diagram.

It summarizes the complete startup process.

---

# Memory Representation

```
JVM

│

├── Environment

├── ApplicationContext

│      ├── StudentController

│      ├── StudentService

│      └── StudentRepository

└── Embedded Tomcat
```

Most important Spring objects are managed inside the ApplicationContext.

---

# Common Startup Errors

Understanding the startup lifecycle helps identify failures quickly.

| Problem | Possible Cause |
|----------|----------------|
| Port already in use | Tomcat startup failed |
| Bean not found | Component not scanned |
| Circular dependency | Bean creation failed |
| Invalid property | Environment configuration issue |
| Database connection error | DataSource initialization failed |

---

# Best Practices

- Keep constructors lightweight.
- Use constructor injection.
- Organize packages properly.
- Read startup logs carefully.
- Avoid unnecessary beans.

---

# Industry Insight

In large enterprise applications,

startup may involve:

- Thousands of beans
- Multiple databases
- Security configuration
- Messaging systems
- Cache initialization

Understanding the startup process helps developers debug production issues much faster.

---

# Interview Corner

### Basic

1. What does `SpringApplication.run()` do?
2. What is the ApplicationContext?
3. When are beans created?

### Intermediate

4. Explain the startup lifecycle.
5. What is the difference between Bean Definition and Bean?
6. When does Tomcat start?

---

# Hands-on Lab

Create a Spring Boot project.

Observe:

- Startup logs
- Startup time
- Tomcat initialization
- Default port
- Bean creation messages

Draw the startup lifecycle based on your observations.

---

# Cheat Sheet

- `SpringApplication.run()` starts the application.
- Environment is prepared first.
- ApplicationContext is created.
- Bean Definitions are loaded.
- Beans are instantiated.
- Dependencies are injected.
- Embedded server starts.
- Application becomes ready.

---

# Summary

`SpringApplication.run()` is the heart of every Spring Boot application. It coordinates the entire startup process—from preparing the environment and creating the IoC container to instantiating beans, applying auto-configuration, and starting the embedded web server. A solid understanding of this lifecycle makes it easier to debug startup issues and understand how Spring Boot applications work internally.

---

# What's Next?

➡ **Chapter 6 – Auto Configuration**

In the next chapter, we'll uncover one of Spring Boot's most powerful features: **Auto Configuration**. You'll learn how Spring Boot decides which beans to create automatically and why adding a single starter dependency can configure an entire technology stack.
