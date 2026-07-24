---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 11
title: Component Scanning - How Spring Finds Your Beans Automatically
difficulty: Intermediate
estimated_time: 210 Minutes
version: 1.0
---

# Chapter 11
# Component Scanning – How Spring Finds Your Beans Automatically

> **"Spring doesn't magically know your classes. It systematically scans your application, identifies candidate components, and registers them as beans."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain Component Scanning.
- Understand how Spring discovers beans.
- Use `@ComponentScan`.
- Understand package scanning.
- Explain startup scanning flow.
- Understand bean registration.
- Optimize package structure.

---

# 📚 Prerequisites

- Spring Beans
- Bean Lifecycle
- Dependency Injection

---

# 🗺 Module Progress

```
Spring Core

↓

Dependency Injection

↓

Constructor Injection

↓

Setter Injection

↓

📍 Component Scanning

↓

Stereotype Annotations

↓

Bean Scopes
```

---

# ❓ The Big Question

Consider this class.

```java
@Service
public class StudentService {

}
```

Question:

Who creates this bean?

You never write:

```java
new StudentService();
```

You never call:

```java
context.register(StudentService.class);
```

Yet the bean exists.

How?

The answer is **Component Scanning**.

---

# 📖 What is Component Scanning?

Component Scanning is the process where Spring searches specified packages, identifies classes marked with component annotations, creates Bean Definitions for them, and registers those definitions with the IoC Container.

Only after registration are beans instantiated.

---

# High-Level Startup Flow

```
Application Starts

↓

SpringApplication.run()

↓

Create ApplicationContext

↓

Read Configuration

↓

Start Component Scan

↓

Find Components

↓

Create Bean Definitions

↓

Register Beans

↓

Instantiate Beans

↓

Application Ready
```

---

# Running Example

Project Structure

```
com.techvidyalaya

├── StudentApplication

├── controller

│     └── StudentController

├── service

│     └── StudentService

├── repository

│     └── StudentRepository

├── config

└── entity
```

Spring starts scanning from the package containing `StudentApplication`.

---

# Why Package Structure Matters

If your main class is located in:

```text
com.techvidyalaya
```

Spring scans:

```
com.techvidyalaya.controller

com.techvidyalaya.service

com.techvidyalaya.repository

com.techvidyalaya.config
```

If a class lives outside this package hierarchy, Spring won't discover it unless explicitly configured.

---

# How Does Spring Know Where to Scan?

Look at the main class.

```java
@SpringBootApplication
public class StudentApplication {

    public static void main(String[] args) {
        SpringApplication.run(StudentApplication.class, args);
    }

}
```

`@SpringBootApplication` includes:

```java
@SpringBootConfiguration

@EnableAutoConfiguration

@ComponentScan
```

This means component scanning is automatically enabled.

---

# @ComponentScan

You can customize scanning.

```java
@ComponentScan(
    basePackages = {
        "com.techvidyalaya",
        "com.shared.library"
    }
)
```

Spring scans every package listed.

---

# Inside Spring 🧠

When the application starts:

```
@SpringBootApplication

↓

@ComponentScan

↓

ClassPath Scanner

↓

Read .class Files

↓

Check Annotations

↓

Candidate Component?

↓

Create BeanDefinition

↓

Register BeanDefinition

↓

Continue Scanning
```

Notice that Spring analyzes compiled `.class` files rather than executing your code.

---

# Internal Classes

During component scanning, several Spring Framework classes work together.

```
ClassPathBeanDefinitionScanner

↓

ClassPathScanningCandidateComponentProvider

↓

BeanDefinitionRegistry

↓

DefaultListableBeanFactory
```

Responsibilities:

| Class | Responsibility |
|--------|----------------|
| ClassPathBeanDefinitionScanner | Coordinates scanning |
| CandidateComponentProvider | Identifies candidate classes |
| BeanDefinitionRegistry | Registers metadata |
| DefaultListableBeanFactory | Stores bean definitions and later creates beans |

Understanding these classes helps explain how Spring transforms annotated classes into managed beans.

---

# Candidate Components

Spring looks for stereotype annotations such as:

```java
@Component

@Service

@Repository

@Controller

@RestController

@Configuration
```

These mark a class as a candidate for registration.

---

# Bean Definition Creation

Spring first creates metadata.

```
StudentService.class

↓

Bean Definition

↓

Bean Name

↓

Scope

↓

Dependencies

↓

Lifecycle Metadata
```

The actual object is created later during bean instantiation.

---

# Component Scanning vs Bean Creation

These are different processes.

```
Component Scan

↓

Bean Definition Registered

↓

ApplicationContext

↓

Instantiate Bean

↓

Dependency Injection

↓

Initialization
```

Scanning discovers *what can become a bean*. Bean creation happens afterward.

---

# How Spring Thinks

```
Found StudentService.class

↓

Annotated with @Service?

↓

Yes

↓

Register Bean Definition

↓

Continue Searching
```

Spring keeps scanning until every configured package has been processed.

---

# Performance Considerations

Component scanning is usually very fast.

However, scanning unnecessarily large package hierarchies can increase startup time.

Good practices:

- Keep the main application class near the project root.
- Avoid scanning unrelated packages.
- Organize code into clear package structures.

---

# Common Mistakes

❌ Placing `StudentApplication` too deep in the package hierarchy.

Example:

```
com.techvidyalaya.controller
```

Now sibling packages like `service` and `repository` may not be scanned.

---

❌ Forgetting to scan an external module.

If shared libraries contain Spring components, include them using `@ComponentScan` or auto-configuration.

---

# 🐞 Debugging Corner

## Error

```text
NoSuchBeanDefinitionException
```

Possible causes:

- Missing stereotype annotation.
- Class outside scanned packages.
- Component scan configuration is incorrect.

Checklist:

✔ Is the class annotated?

✔ Is it under the scanned package?

✔ Is the application scanning the correct base package?

---

# 🏢 Industry Insight

Large organizations often split applications into multiple modules:

```
common

billing

orders

payments

inventory
```

Each module contributes Spring components.

Teams typically rely on well-defined package structures or Spring Boot auto-configuration to ensure components are discovered consistently.

---

# 🎤 Interview Corner

### Beginner

1. What is Component Scanning?
2. Why is it required?
3. Which annotation enables it?

---

### Intermediate

4. What does `@SpringBootApplication` include?
5. What is the purpose of `@ComponentScan`?
6. Why does package structure matter?

---

### Advanced

7. Explain the complete component scanning process.
8. What is a `BeanDefinition`?
9. Which internal Spring classes participate in scanning?

---

# 🧪 Hands-on Lab

## Objective

Observe Component Scanning.

### Tasks

1. Create:

```
StudentController

StudentService

StudentRepository
```

2. Start the application.

3. Confirm all beans are created.

4. Move `StudentRepository` outside the root package.

5. Restart the application.

6. Observe the startup failure.

7. Fix it using:

```java
@ComponentScan(
    basePackages = {
        "com.techvidyalaya",
        "com.external"
    }
)
```

8. Verify the application starts successfully.

---

# 🎯 Mini Challenge

1. What is Component Scanning?
2. How does Spring know where to scan?
3. What is a candidate component?
4. Why is package structure important?
5. What is the difference between scanning and bean creation?

---

# 📌 Chapter Summary

Component Scanning is the discovery mechanism that allows Spring to automatically identify candidate classes, register them as bean definitions, and prepare them for dependency injection. By combining `@SpringBootApplication`, `@ComponentScan`, and stereotype annotations, Spring Boot eliminates much of the manual configuration required in traditional Java applications.

Understanding component scanning also helps developers troubleshoot missing beans, design better package structures, and appreciate how Spring initializes an application.

---

# 📋 Cheat Sheet

| Concept | Description |
|---------|-------------|
| Component Scan | Discovers candidate classes |
| Candidate Component | Annotated Spring-managed class |
| BeanDefinition | Metadata describing a bean |
| @ComponentScan | Configures packages to scan |
| Default Entry Point | Package of the main application class |

---

# 🧠 Quiz

1. What is Component Scanning?
2. Which annotation enables it?
3. What is the role of `BeanDefinition`?
4. Why should the main application class be placed near the project root?
5. What internal class coordinates component scanning?
6. What happens if a component is outside the scanned packages?

---

# 📖 What's Next?

➡ **Chapter 12 – Stereotype Annotations: Understanding @Component, @Service, @Repository, @Controller, @RestController, and @Configuration**

You'll learn why Spring provides multiple stereotype annotations, how each communicates intent, what additional behavior some of them provide, and how to choose the right annotation for every layer of your application.
