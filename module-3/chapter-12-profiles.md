---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 12
title: Spring Profiles – Managing Multiple Environments
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_coding_time: 35 Minutes
estimated_lab_time: 25 Minutes
version: 1.0
---

# Chapter 12
# Spring Profiles – Managing Multiple Environments

> **"Write your application once, run it in multiple environments with different configurations."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what Spring Profiles are.
- Learn why profiles are essential in enterprise applications.
- Create profile-specific configuration files.
- Activate profiles using different methods.
- Create profile-specific beans.
- Follow best practices for managing multiple environments.

---

# Introduction

Imagine you've completed the Student Management System.

During development, you connect to:

- Local MySQL Database
- Debug Logging
- Test Email Server

Before deployment, the application must connect to:

- Production Database
- Production Email Service
- Optimized Logging
- Production API Keys

Should you edit the configuration file every time?

**No.**

Spring Profiles allow the same application to behave differently depending on the active environment.

---

# What is a Spring Profile?

A **Spring Profile** represents a specific runtime environment.

Each profile contains configuration for a particular environment.

Common profiles include:

- `dev`
- `test`
- `staging`
- `prod`

Only one or more active profiles are loaded when the application starts.

---

# Why Do We Need Profiles?

Without Profiles:

```
One Configuration File

↓

Manual Changes

↓

High Risk of Errors

↓

Frequent Deployment Issues
```

With Profiles:

```
Development

↓

Testing

↓

Staging

↓

Production

↓

Same Application Code
```

Profiles eliminate manual configuration changes.

---

# Profile Configuration Files

Spring Boot supports dedicated configuration files for each profile.

```
application.properties

application-dev.properties

application-test.properties

application-staging.properties

application-prod.properties
```

Each file stores configuration specific to its environment.

---

# Example Configuration

### application.properties

```properties
spring.application.name=StudentManagement
```

Shared configuration for all environments.

---

### application-dev.properties

```properties
server.port=8080

logging.level.root=DEBUG

spring.datasource.url=jdbc:mysql://localhost:3306/studentdb
```

---

### application-prod.properties

```properties
server.port=80

logging.level.root=INFO

spring.datasource.url=jdbc:mysql://prod-db:3306/studentdb
```

The application automatically loads the appropriate file based on the active profile.

---

# Activating a Profile

## Using application.properties

```properties
spring.profiles.active=dev
```

---

## Using Command-Line Arguments

```bash
java -jar app.jar --spring.profiles.active=prod
```

---

## Using Environment Variables

```text
SPRING_PROFILES_ACTIVE=prod
```

This approach is commonly used in Docker, Kubernetes, and cloud deployments.

---

# Default Profile

If no profile is activated,

Spring Boot loads only:

```
application.properties
```

This file should contain common configuration shared across all environments.

---

# Profile-Specific Beans

Profiles can also control bean creation.

Example:

```java
@Service
@Profile("dev")
public class MockEmailService {

}
```

---

```java
@Service
@Profile("prod")
public class RealEmailService {

}
```

When running in development:

```
MockEmailService
```

When running in production:

```
RealEmailService
```

Spring creates only the bean that matches the active profile.

---

# Configuration Loading Flow

```
Application Starts

↓

Load application.properties

↓

Identify Active Profile

↓

Load Profile Configuration

↓

Override Common Settings

↓

Application Ready
```

---

# Typical Enterprise Environment

| Environment | Purpose |
|-------------|---------|
| Development | Local development |
| Testing | Automated and manual testing |
| Staging | Pre-production validation |
| Production | Live customer environment |

Each environment uses different configuration while sharing the same application code.

---

# Benefits of Spring Profiles

- Environment-specific configuration
- Cleaner configuration management
- No source code changes
- Easier deployment
- Better security
- Reduced deployment mistakes

---

# Common Mistakes

❌ Putting production credentials inside `application.properties`.

❌ Using development settings in production.

❌ Forgetting to activate the correct profile.

❌ Duplicating the same configuration across multiple profile files.

---

# Best Practices

- Keep common settings in `application.properties`.
- Store only environment-specific values inside profile files.
- Never commit passwords or API keys to Git.
- Use meaningful profile names (`dev`, `test`, `staging`, `prod`).
- Validate every profile before deployment.

---

# Industry Insight

Almost every enterprise Spring Boot application uses profiles.

Example deployment pipeline:

```
Developer

↓

Development

↓

Testing

↓

Staging

↓

Production
```

Only the configuration changes.

The application artifact remains exactly the same.

This approach supports modern CI/CD pipelines and reliable deployments.

---

# Interview Corner

## Basic

1. What is a Spring Profile?
2. Why are Profiles used?
3. How do you activate a profile?

---

## Intermediate

4. Explain `@Profile`.
5. How are profile-specific configuration files loaded?
6. What happens if no profile is active?

---

## Advanced

7. How would you configure different databases for development and production?
8. Why should production secrets not be stored in profile files committed to Git?
9. Explain how Spring Profiles simplify CI/CD deployments.

---

# Hands-on Lab

## Objective

Configure and test multiple application environments.

### Tasks

1. Create:
   - `application-dev.properties`
   - `application-test.properties`
   - `application-prod.properties`
2. Configure different server ports.
3. Configure different logging levels.
4. Activate each profile.
5. Create two implementations of an Email Service using `@Profile`.
6. Verify which bean is created for each environment.

---

# Cheat Sheet

- Profiles manage environment-specific configuration.
- Use `application-{profile}.properties`.
- Activate using:
  - `spring.profiles.active`
  - Environment Variables
  - Command-Line Arguments
- Use `@Profile` for environment-specific beans.
- Keep shared configuration in `application.properties`.

---

# Summary

Spring Profiles allow a single Spring Boot application to run in multiple environments without changing its source code. By separating environment-specific settings into dedicated profile files, developers can safely manage development, testing, staging, and production configurations while keeping deployments simple, secure, and reliable.

---

# What's Next?

➡ **Chapter 13 – Spring Boot Actuator – Monitoring and Managing Applications**

In the next chapter, you'll learn how Spring Boot Actuator exposes production-ready endpoints for monitoring application health, metrics, configuration, and runtime information.
