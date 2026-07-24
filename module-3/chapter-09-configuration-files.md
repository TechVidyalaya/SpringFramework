---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 9
title: Configuration Files – Customizing Spring Boot Applications
difficulty: Beginner
estimated_reading_time: 45 Minutes
estimated_coding_time: 35 Minutes
estimated_lab_time: 20 Minutes
version: 1.0
---

# Chapter 9
# Configuration Files – Customizing Spring Boot Applications

> **"Good applications don't hardcode values—they read them from configuration."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand why configuration files are important.
- Learn the purpose of `application.properties` and `application.yml`.
- Configure common Spring Boot settings.
- Read custom properties in your application.
- Organize configuration for maintainability.
- Follow configuration best practices.

---

# Introduction

Imagine your application contains the following code:

```java
String databaseUrl = "jdbc:mysql://localhost:3306/studentdb";
```

What happens if you deploy the application to another machine?

You'll have to modify the source code and rebuild the application.

A better approach is to keep such values outside the code.

That's exactly what Spring Boot configuration files provide.

---

# What is a Configuration File?

A configuration file stores application settings separately from the source code.

Examples include:

- Server Port
- Database URL
- Username
- Password
- Logging Level
- API Keys
- Feature Flags

Changing these values does not require modifying Java code.

---

# Default Configuration Files

Spring Boot supports two main configuration formats:

```
application.properties
```

and

```
application.yml
```

Both serve the same purpose.

Choose one style and use it consistently.

---

# application.properties Example

```properties
server.port=8081

spring.application.name=student-management

server.servlet.context-path=/api
```

Simple and familiar for most Java developers.

---

# application.yml Example

```yaml
server:
  port: 8081
  servlet:
    context-path: /api

spring:
  application:
    name: student-management
```

YAML uses indentation instead of key-value pairs.

---

# Properties vs YAML

| application.properties | application.yml |
|-------------------------|-----------------|
| Simple key-value format | Hierarchical structure |
| Easy for beginners | Better for large projects |
| Widely used | More readable for nested configuration |

Both are fully supported by Spring Boot.

---

# Common Configuration Properties

## Application Name

```properties
spring.application.name=StudentManagement
```

---

## Server Port

```properties
server.port=9090
```

---

## Context Path

```properties
server.servlet.context-path=/student-api
```

Application URL becomes:

```
http://localhost:9090/student-api
```

---

## Banner Mode

Disable the Spring Boot banner:

```properties
spring.main.banner-mode=off
```

---

## Logging Level

```properties
logging.level.root=INFO
```

For debugging:

```properties
logging.level.root=DEBUG
```

---

# Reading Custom Properties

You can define your own properties.

Example:

```properties
college.name=TechVidyalaya
```

Read it using `@Value`.

```java
@Value("${college.name}")
private String collegeName;
```

Spring injects the configured value automatically.

---

# Using @ConfigurationProperties

When related properties grow in number, grouping them is a better approach.

Example:

```properties
college.name=TechVidyalaya
college.city=Bhubaneswar
college.established=2026
```

These properties can be mapped to a Java class using `@ConfigurationProperties`.

This approach improves readability and maintainability.

---

# Configuration Loading Order

Spring Boot loads configuration from multiple sources.

A simplified order is:

```
Command Line Arguments

↓

Environment Variables

↓

application.properties

↓

Default Values
```

Higher-priority sources override lower-priority ones.

---

# Best Practices

- Keep configuration outside the code.
- Never hardcode passwords or API keys.
- Group related properties.
- Use meaningful property names.
- Keep configuration files clean and organized.

---

# Common Mistakes

❌ Hardcoding database credentials.

❌ Mixing `.properties` and `.yml` in the same project unnecessarily.

❌ Storing sensitive information in Git repositories.

❌ Using unclear property names.

---

# Industry Insight

In enterprise projects, configuration controls almost everything:

- Database connections
- Message brokers
- Cache servers
- External APIs
- Logging
- Security
- Feature toggles

Developers often change configuration without modifying the application code.

---

# Interview Corner

### Basic

1. What is the purpose of `application.properties`?
2. Difference between `.properties` and `.yml`?
3. How do you change the server port?

### Intermediate

4. What is `@Value`?
5. Why use `@ConfigurationProperties`?
6. How does Spring Boot load configuration?

### Advanced

7. Which configuration source has higher priority?
8. Why should secrets never be stored in configuration files committed to Git?

---

# Hands-on Lab

## Objective

Configure a Spring Boot application.

### Tasks

1. Change the application name.
2. Change the server port to `9090`.
3. Add a custom context path.
4. Create a custom property:
   ```properties
   college.name=TechVidyalaya
   ```
5. Read the property using `@Value`.
6. Display it through a REST endpoint.

---

# Cheat Sheet

- `application.properties` stores application configuration.
- `.properties` and `.yml` provide the same functionality.
- Use `@Value` for simple property injection.
- Use `@ConfigurationProperties` for grouped settings.
- Avoid hardcoding environment-specific values.

---

# Summary

Configuration files are a fundamental part of every Spring Boot application. They allow developers to customize application behavior without changing source code, making applications easier to maintain, deploy, and manage across different environments. By separating configuration from code, Spring Boot promotes flexibility, security, and clean application design.

---

# What's Next?

➡ **Chapter 10 – Profiles – Managing Multiple Environments**

In the next chapter, you'll learn how Spring Boot Profiles allow the same application to run with different configurations for development, testing, and production environments.
