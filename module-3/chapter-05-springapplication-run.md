---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 5
title: Inside SpringApplication.run() – The Complete Startup Lifecycle
difficulty: Intermediate
estimated_reading_time: 90 Minutes
estimated_coding_time: 60 Minutes
estimated_lab_time: 45 Minutes
total_estimated_time: 195 Minutes
version: 1.0
---

# Chapter 5
# Inside `SpringApplication.run()` – The Complete Startup Lifecycle

> **"Every Spring Boot application begins with one method call. Behind that single line lies a sophisticated startup engine responsible for preparing the environment, building the IoC container, configuring infrastructure, and launching your application."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain the purpose of `SpringApplication.run()`.
- Describe each startup phase.
- Understand how the `ApplicationContext` is created.
- Explain when bean definitions are loaded.
- Understand when auto-configuration occurs.
- Identify where the embedded web server starts.
- Diagnose startup failures using the startup lifecycle.

---

# 📖 Estimated Study Time

| Activity | Time |
|----------|------|
| Reading | 90 min |
| Coding | 60 min |
| Hands-on Lab | 45 min |
| Quiz & Revision | 20 min |
| **Total** | **~3 Hours** |

---

# 📚 Prerequisites

- Chapters 1–4
- Spring Core (Module 2)

---

# 🗺 Module Roadmap

```
Introduction

↓

Why Spring Boot

↓

Architecture

↓

@SpringBootApplication

↓

SpringApplication.run() ← You Are Here

↓

Auto Configuration

↓

Starter Dependencies

↓

Embedded Server
```

---

# 🏢 Industry Story

Your production application fails to start.

The log contains:

```
APPLICATION FAILED TO START
```

Where do you begin?

Did the application fail while:

- Reading configuration?
- Creating the ApplicationContext?
- Loading beans?
- Starting Tomcat?
- Connecting to the database?

Without understanding the startup lifecycle, debugging becomes guesswork.

This chapter provides that understanding.

---

# ❓ What is `SpringApplication.run()`?

Every Spring Boot application starts with:

```java
@SpringBootApplication
public class StudentApplication {

    public static void main(String[] args) {
        SpringApplication.run(StudentApplication.class, args);
    }

}
```

Although it appears to be a single method call, it coordinates the entire startup process.

---

# 🏗 High-Level Startup Lifecycle

```
main()

↓

SpringApplication.run()

↓

Create SpringApplication

↓

Prepare Environment

↓

Print Banner

↓

Create ApplicationContext

↓

Load Bean Definitions

↓

Apply Auto Configuration

↓

Instantiate Beans

↓

Dependency Injection

↓

Execute Initializers

↓

Refresh Context

↓

Start Embedded Server

↓

Run Application Runners

↓

Application Ready
```

This is the roadmap for the rest of the chapter.

---

# Phase 1 – Create SpringApplication

The framework creates a `SpringApplication` object using the main application class.

Responsibilities include:

- Determining the application type (Servlet, Reactive, or Non-Web)
- Registering listeners
- Preparing startup infrastructure

---

# Phase 2 – Prepare the Environment

Spring collects configuration from multiple sources.

Examples:

- `application.properties`
- `application.yml`
- JVM system properties
- Environment variables
- Command-line arguments

Priority rules determine which value wins when the same property appears in multiple places.

---

# Phase 3 – Print the Banner

By default, Spring Boot displays the startup banner.

Example:

```
  ____             _
 / ___| _ __  _ __(_)_ __   __ _
 \___ \| '_ \| '__| | '_ \ / _` |
  ___) | |_) | |  | | | | | (_| |
 |____/| .__/|_|  |_|_| |_|\__, |
       |_|                 |___/
```

The banner is optional and can be customized or disabled.

---

# Phase 4 – Create the ApplicationContext

Spring Boot creates the appropriate `ApplicationContext`.

For web applications:

```
AnnotationConfigServletWebServerApplicationContext
```

Responsibilities:

- Bean registry
- Dependency Injection
- Lifecycle management
- Event publishing

This becomes the central container for the application.

---

# Phase 5 – Load Bean Definitions

Spring scans the configured packages.

```
@Component

@Service

@Repository

@Controller

@Configuration
```

Instead of creating objects immediately, Spring first creates **Bean Definitions**.

Think of a Bean Definition as a blueprint describing how to create a bean.

---

# Bean Definition vs Bean

```
Bean Definition

↓

Blueprint

↓

Spring Creates Object

↓

Bean Instance
```

This distinction is important for understanding the startup process.

---

# Phase 6 – Apply Auto Configuration

Spring Boot evaluates the classpath and configuration properties.

Examples:

- Is Spring MVC available?
- Is Jackson present?
- Is JPA present?
- Is Tomcat available?

If the required conditions are met, Spring Boot automatically registers additional beans.

We'll examine this mechanism in the next chapter.

---

# Phase 7 – Instantiate Beans

The IoC container begins creating singleton beans.

Typical order:

```
Configuration Beans

↓

Infrastructure Beans

↓

Repositories

↓

Services

↓

Controllers
```

Dependencies are resolved recursively.

---

# Phase 8 – Dependency Injection

After bean creation, Spring injects dependencies.

Example:

```
StudentController

↓

StudentService

↓

StudentRepository
```

Constructor injection is performed before the application becomes available.

---

# Phase 9 – Refresh the Context

The `refresh()` method finalizes the container.

Key responsibilities:

- Initialize remaining singleton beans.
- Register event listeners.
- Prepare lifecycle processors.
- Publish startup events.

This is one of the most important methods in the Spring Framework.

---

# Phase 10 – Start Embedded Server

If the application is a web application:

```
Embedded Tomcat

↓

Bind Port

↓

Register DispatcherServlet

↓

Start Listening
```

The application is now capable of handling HTTP requests.

---

# Phase 11 – Execute Startup Callbacks

Spring invokes:

- `ApplicationRunner`
- `CommandLineRunner`

These are commonly used for:

- Data initialization
- Cache warm-up
- Startup validation

---

# Phase 12 – Application Ready

Finally:

```
Started StudentApplication in 2.314 seconds
```

The application begins serving client requests.

---

# 🧠 Complete Startup Flow

```
main()

↓

SpringApplication.run()

↓

Environment

↓

ApplicationContext

↓

Bean Definitions

↓

Auto Configuration

↓

Bean Creation

↓

Dependency Injection

↓

Context Refresh

↓

Embedded Tomcat

↓

ApplicationRunner

↓

READY
```

This sequence is one of the most important diagrams in the entire course.

---

# 🧠 Memory Representation

```
JVM Heap

+------------------------------------------------+

Environment

ApplicationContext

BeanFactory

Bean Definitions

StudentController

StudentService

StudentRepository

DispatcherServlet

Tomcat

+------------------------------------------------+
```

The `ApplicationContext` holds references to most application components.

---

# 🕒 Timeline

```
Run Application

↓

Read Configuration

↓

Create Context

↓

Load Definitions

↓

Create Beans

↓

Inject Dependencies

↓

Start Server

↓

Ready
```

---

# 💡 Behind the Scenes

`SpringApplication.run()` is a façade.

Internally, it coordinates dozens of framework classes including:

- Environment processors
- Bean definition readers
- Context refresh logic
- Event publishers
- Auto-configuration import selectors
- Embedded server factories

Understanding this orchestration helps explain why Spring Boot startup is both powerful and extensible.

---

# 🧩 Myth vs Reality

### Myth

`SpringApplication.run()` simply starts Tomcat.

**Reality**

Starting the web server is only one stage in a much larger startup process.

---

### Myth

Beans are created as soon as they are discovered.

**Reality**

Spring first registers bean definitions, then instantiates beans during context refresh.

---

### Myth

Auto-configuration happens after the application is running.

**Reality**

Auto-configuration occurs during startup, before the application is ready to serve requests.

---

# 🏭 Industry Insight

Large enterprise applications may:

- Load thousands of beans.
- Execute dozens of auto-configurations.
- Initialize database pools.
- Register messaging clients.
- Warm caches.
- Validate external services.

Understanding the startup lifecycle enables engineers to optimize startup time and troubleshoot initialization failures.

---

# ⚡ Performance Notes

Factors that affect startup time include:

- Bean count.
- Classpath size.
- Reflection.
- Database initialization.
- Flyway/Liquibase migrations.
- External service calls.

Monitoring startup logs can reveal performance bottlenecks.

---

# 🐞 Debugging Corner

When startup fails:

1. Read the first exception, not the last.
2. Determine which startup phase failed.
3. Identify the bean involved.
4. Check configuration properties.
5. Verify dependency versions.
6. Enable debug logging if necessary.

Understanding the lifecycle helps narrow the investigation quickly.

---

# ❌ Common Mistakes

- Assuming bean creation happens before configuration loading.
- Ignoring startup logs.
- Confusing bean definitions with bean instances.
- Placing expensive operations inside constructors.
- Treating startup failures as random.

---

# ✅ Best Practices

- Keep constructors lightweight.
- Use `ApplicationRunner` for initialization logic.
- Organize configuration clearly.
- Understand startup logs.
- Avoid unnecessary beans.

---

# 🎤 Interview Corner

### Beginner

1. What does `SpringApplication.run()` do?
2. What is the role of the `ApplicationContext`?
3. When is the embedded server started?

### Intermediate

4. Explain the startup lifecycle.
5. What is the difference between bean definitions and bean instances?
6. What is context refresh?

### Advanced

7. Describe the complete startup sequence.
8. How would you debug a startup failure?
9. Which startup phase is responsible for auto-configuration?

---

# 🧪 Hands-on Lab

## Objective

Observe the startup lifecycle.

### Tasks

1. Create a Spring Boot application.
2. Run it.
3. Study the startup logs.
4. Identify when Tomcat starts.
5. Add an `ApplicationRunner`.
6. Observe when it executes.
7. Add a custom banner.
8. Measure startup time.

---

# 🎯 Mini Challenge

1. Draw the startup lifecycle from memory.
2. Explain the purpose of each startup phase.
3. Why are bean definitions created before bean instances?
4. What happens during context refresh?
5. Where does auto-configuration fit into the lifecycle?

---

# 📋 Cheat Sheet

- `SpringApplication.run()` orchestrates the entire startup process.
- The Environment is prepared before the `ApplicationContext`.
- Bean definitions are loaded before beans are instantiated.
- Auto-configuration is applied during startup.
- The embedded server starts after the context is refreshed.
- `ApplicationRunner` executes after the application is ready.

---

# 🧠 Knowledge Graph

```
SpringApplication.run()

├── Environment
├── ApplicationContext
├── Bean Definitions
├── Bean Creation
├── Dependency Injection
├── Auto Configuration
├── Context Refresh
├── Embedded Server
└── ApplicationRunner
```

---

# 📝 Quiz

1. What are the major phases of the Spring Boot startup lifecycle?
2. What is the purpose of the `Environment`?
3. Why are bean definitions created before bean instances?
4. What does the `refresh()` method accomplish?
5. When is the embedded web server started?

---

# 📌 Chapter Summary

`SpringApplication.run()` is the orchestration engine of a Spring Boot application. It prepares the environment, creates the `ApplicationContext`, registers bean definitions, applies auto-configuration, instantiates beans, refreshes the context, starts the embedded server, and finally executes startup callbacks. Understanding this lifecycle is essential for debugging, performance tuning, and mastering Spring Boot internals.

---

# 📖 What's Next?

➡ **Chapter 6 – Auto Configuration: The Magic Behind Spring Boot**

You've seen *where* auto-configuration fits into the startup lifecycle. In the next chapter, we'll uncover **how** Spring Boot decides which beans to create, how conditional configuration works, and why simply adding a dependency can enable an entire feature set.
