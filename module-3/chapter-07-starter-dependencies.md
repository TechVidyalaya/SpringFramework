---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 7
title: Starter Dependencies – Simplifying Dependency Management
difficulty: Beginner
estimated_reading_time: 40 Minutes
estimated_coding_time: 25 Minutes
estimated_lab_time: 20 Minutes
version: 1.0
---

# Chapter 7
# Starter Dependencies – Simplifying Dependency Management

> **"Instead of searching for dozens of compatible libraries, Spring Boot lets you add a single starter dependency and begin building your application."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what Starter Dependencies are.
- Explain why Spring Boot introduced starters.
- Learn how starters simplify dependency management.
- Identify commonly used Spring Boot starters.
- Understand transitive dependencies.
- Choose the right starter for different application types.

---

# Introduction

Imagine you're building a REST API.

Without Spring Boot, you would need to manually add dependencies for:

- Spring MVC
- Jackson
- Validation
- Embedded Tomcat
- Logging
- Spring Core

You also need to ensure all versions are compatible.

Spring Boot simplifies this by providing **Starter Dependencies**.

---

# What is a Starter Dependency?

A Starter Dependency is a predefined collection of compatible libraries designed for a specific purpose.

Instead of adding many dependencies individually, you add one starter.

Example:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This single dependency brings everything needed to build a web application.

---

# Why Starter Dependencies Were Introduced

Before Spring Boot:

```
Developer

↓

Search Maven Repository

↓

Choose Versions

↓

Resolve Conflicts

↓

Fix Compatibility Issues

↓

Start Development
```

With Spring Boot:

```
Add Starter

↓

Build Project

↓

Start Development
```

This significantly improves productivity.

---

# How Starter Dependencies Work

A starter does not contain application code.

Instead, it references multiple compatible dependencies.

```
spring-boot-starter-web

│

├── Spring MVC

├── Spring Core

├── Spring Boot

├── Embedded Tomcat

├── Jackson

├── Validation

└── Logging
```

Maven automatically downloads all required libraries.

---

# Transitive Dependencies

When Maven downloads one dependency, it may automatically download other required dependencies.

This is known as **Transitive Dependency**.

Example:

```
spring-boot-starter-web

↓

spring-webmvc

↓

spring-web

↓

spring-core

↓

spring-beans
```

You only add one dependency.

Maven handles the rest.

---

# Common Spring Boot Starters

| Starter | Purpose |
|----------|---------|
| spring-boot-starter-web | Build REST APIs and web applications |
| spring-boot-starter-data-jpa | Database access using JPA and Hibernate |
| spring-boot-starter-security | Authentication and authorization |
| spring-boot-starter-test | Unit and integration testing |
| spring-boot-starter-validation | Bean validation |
| spring-boot-starter-actuator | Monitoring and health checks |
| spring-boot-starter-cache | Caching support |
| spring-boot-starter-websocket | WebSocket applications |
| spring-boot-starter-mail | Email integration |

---

# Starter Dependencies and Auto Configuration

Starter Dependencies and Auto Configuration work together.

```
Add Starter Dependency

↓

Libraries Added

↓

Spring Boot Detects Libraries

↓

Auto Configuration Runs

↓

Beans Created

↓

Application Ready
```

Without starters, many auto-configurations would never be triggered.

---

# Starter Parent

Most Spring Boot projects also include:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
</parent>
```

This parent project provides:

- Compatible dependency versions
- Plugin management
- Build configuration
- Default Maven settings

You usually don't need to specify versions for Spring Boot libraries.

---

# Choosing the Right Starter

Examples:

### Building REST APIs

```
spring-boot-starter-web
```

---

### Database Applications

```
spring-boot-starter-data-jpa
```

---

### Secure Applications

```
spring-boot-starter-security
```

---

### Microservices

```
spring-boot-starter-web

+

spring-boot-starter-actuator
```

---

# Best Practices

- Use starter dependencies whenever possible.
- Avoid adding duplicate libraries.
- Let Spring Boot manage dependency versions.
- Remove unused starters to reduce application size.
- Use only the starters required for your project.

---

# Common Mistakes

❌ Adding individual Spring libraries instead of using starters.

❌ Specifying unnecessary versions for Spring Boot dependencies.

❌ Including multiple web starters unnecessarily.

❌ Adding unused starters that increase startup time.

---

# Industry Insight

Enterprise applications often use multiple starters together.

Example:

```
Student Management System

↓

Web Starter

↓

Data JPA Starter

↓

Validation Starter

↓

Security Starter

↓

Actuator Starter
```

Each starter contributes a specific capability while remaining compatible with the others.

---

# Interview Corner

### Basic

1. What is a Starter Dependency?
2. Why are Starter Dependencies useful?
3. What is `spring-boot-starter-web`?

### Intermediate

4. Explain transitive dependencies.
5. What is the purpose of `spring-boot-starter-parent`?
6. How do Starter Dependencies work with Auto Configuration?

### Advanced

7. How would you troubleshoot dependency conflicts?
8. Can you create your own starter dependency?

---

# Hands-on Lab

## Objective

Explore Starter Dependencies.

### Tasks

1. Create a Spring Boot project.
2. Add:
   - `spring-boot-starter-web`
   - `spring-boot-starter-data-jpa`
3. Refresh Maven.
4. View the Maven dependency tree.
5. Identify transitive dependencies.
6. Remove one starter and observe the changes.

---

# Cheat Sheet

- Starter Dependencies simplify dependency management.
- One starter includes multiple compatible libraries.
- Maven downloads transitive dependencies automatically.
- Starter Dependencies work closely with Auto Configuration.
- Use the appropriate starter based on application requirements.

---

# Summary

Starter Dependencies are one of Spring Boot's biggest productivity features. They eliminate the need to manually manage dozens of library versions by providing carefully tested and compatible dependency sets. Combined with Auto Configuration, they allow developers to create enterprise applications with minimal setup while maintaining consistency and stability.

---

# What's Next?

➡ **Chapter 8 – Embedded Web Servers – Running Without External Tomcat**

In the next chapter, you'll learn how Spring Boot starts an embedded web server like Tomcat, Jetty, or Undertow, eliminating the need to deploy WAR files to external application servers.
