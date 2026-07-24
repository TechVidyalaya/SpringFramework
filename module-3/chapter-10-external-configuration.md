---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 10
title: External Configuration – Managing Configuration Outside the Application
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_coding_time: 30 Minutes
estimated_lab_time: 25 Minutes
version: 1.0
---

# Chapter 10
# External Configuration – Managing Configuration Outside the Application

> **"A well-designed application separates configuration from code, making it flexible, secure, and easy to deploy across different environments."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand the concept of External Configuration.
- Explain why configuration should be separated from application code.
- Identify the various configuration sources supported by Spring Boot.
- Understand configuration precedence (priority).
- Override configuration using environment variables, JVM properties, and command-line arguments.
- Follow best practices for secure and maintainable configuration management.

---

# Introduction

Imagine you've built a Student Management System.

Initially, your application contains the following configuration:

```java
String databaseUrl = "jdbc:mysql://localhost:3306/studentdb";
String username = "root";
String password = "root123";
```

Everything works perfectly on your local machine.

Now imagine deploying the same application to:

- Testing Environment
- Production Server
- Cloud Platform

Each environment has different:

- Database URL
- Credentials
- API Keys
- Server Port
- Logging Level

Should you modify the source code every time?

**Absolutely not.**

Instead, the configuration should come from outside the application.

This concept is called **External Configuration**.

---

# What is External Configuration?

External Configuration means storing application settings outside the application code.

Instead of hardcoding values, Spring Boot reads them from external sources during application startup.

Examples include:

- Database URL
- Database Username
- Database Password
- Server Port
- Logging Level
- API Keys
- Email Configuration
- Cache Settings

This makes the application portable and environment-independent.

---

# Why External Configuration?

Without external configuration:

- Source code changes for every environment
- Frequent rebuilds
- Difficult deployments
- Increased security risks
- Hardcoded credentials

With external configuration:

- Same application runs everywhere
- No code changes required
- Better security
- Easier maintenance
- Faster deployments

---

# Configuration Sources

Spring Boot can read configuration from multiple sources.

Common sources include:

- `application.properties`
- `application.yml`
- Environment Variables
- JVM System Properties
- Command-Line Arguments

All these sources can define the same property.

Spring Boot automatically decides which value to use.

---

# Configuration Priority

Spring Boot follows a precedence order when the same property is defined in multiple places.

A simplified order is:

```
Command-Line Arguments
        │
        ▼
Environment Variables
        │
        ▼
JVM System Properties
        │
        ▼
application.properties
        │
        ▼
Default Values
```

Higher-priority sources override lower-priority ones.

---

# Using application.properties

Example:

```properties
server.port=8080

spring.application.name=StudentManagement

logging.level.root=INFO
```

These values are loaded automatically during application startup.

---

# Using Environment Variables

Suppose you don't want to modify the configuration file.

You can define an environment variable.

Example:

```text
SERVER_PORT=9090
```

When the application starts, Spring Boot reads this value and uses port **9090**.

Environment variables are commonly used in:

- Docker
- Kubernetes
- Cloud Platforms
- CI/CD Pipelines

---

# Using JVM System Properties

Configuration can also be passed while starting the JVM.

Example:

```bash
java -Dserver.port=9090 -jar student-management.jar
```

Spring Boot reads the JVM property and overrides the value from the configuration file.

---

# Using Command-Line Arguments

Command-line arguments have the highest priority.

Example:

```bash
java -jar student-management.jar --server.port=8081
```

Regardless of the value inside `application.properties`, the application starts on port **8081**.

---

# Real-World Example

Suppose your application contains:

```properties
server.port=8080
```

Your DevOps engineer starts the application using:

```bash
java -jar app.jar --server.port=9090
```

Result:

```
Application starts on Port 9090
```

Spring Boot always uses the highest-priority configuration.

---

# Configuration Flow

```
Application Starts
        │
        ▼
Read Command-Line Arguments
        │
        ▼
Read Environment Variables
        │
        ▼
Read JVM Properties
        │
        ▼
Read application.properties
        │
        ▼
Use Highest Priority Value
```

---

# Where is External Configuration Used?

External Configuration is widely used for:

- Database Connection
- Redis Configuration
- Kafka Servers
- SMTP Settings
- JWT Secret Keys
- Cloud Storage
- Third-Party APIs
- Feature Flags

---

# Best Practices

- Never hardcode passwords.
- Store secrets outside the source code.
- Keep environment-specific values external.
- Use meaningful property names.
- Version configuration carefully.
- Use environment variables for sensitive information.

---

# Common Mistakes

❌ Hardcoding credentials.

❌ Storing API keys in Git repositories.

❌ Maintaining separate codebases for different environments.

❌ Forgetting configuration precedence.

❌ Mixing development and production settings.

---

# Industry Insight

Modern deployment platforms such as:

- Docker
- Kubernetes
- AWS
- Azure
- Google Cloud

heavily rely on external configuration.

The same application artifact is promoted across environments while only the configuration changes.

This approach improves consistency, security, and deployment speed.

---

# Interview Corner

## Basic

1. What is External Configuration?
2. Why is External Configuration important?
3. Name some configuration sources supported by Spring Boot.

---

## Intermediate

4. What is configuration precedence?
5. How do Environment Variables override configuration files?
6. Which configuration source has the highest priority?

---

## Advanced

7. Why are environment variables preferred for secrets?
8. How would you configure different databases without changing code?
9. Explain how Spring Boot resolves configuration conflicts.

---

# Hands-on Lab

## Objective

Understand how Spring Boot loads configuration from multiple sources.

### Tasks

1. Create an application with:

```properties
server.port=8080
```

2. Run the application.

3. Override the port using:

```bash
-Dserver.port=9090
```

4. Override again using:

```bash
--server.port=8081
```

5. Observe which value Spring Boot uses.

6. Explain why the application started on that port.

---

# Cheat Sheet

- External Configuration separates configuration from code.
- Spring Boot supports multiple configuration sources.
- Command-line arguments have the highest priority.
- Environment Variables are ideal for deployment environments.
- Avoid storing secrets in source code.

---

# Summary

External Configuration is a core feature of Spring Boot that allows applications to remain flexible, secure, and environment-independent. Instead of modifying source code for different deployments, Spring Boot reads configuration from multiple external sources and applies a well-defined precedence order. This approach enables the same application to run across development, testing, staging, and production environments with only configuration changes.

---

# What's Next?

➡ **Chapter 11 – Spring Profiles – Managing Multiple Environments**

In the next chapter, you'll learn how Spring Profiles build on external configuration to provide separate configurations for development, testing, staging, and production environments while using the same application code.
