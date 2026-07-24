---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 15
title: Profiles & Environment - Building Applications for Multiple Environments
difficulty: Intermediate
estimated_time: 270 Minutes
version: 1.0
---

# Chapter 15
# Profiles & Environment – Building Applications for Development, Testing, and Production

> **"Professional applications don't change code between environments—they change configuration."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Spring's Environment abstraction.
- Use `application.properties` and `application.yml`.
- Inject configuration using `@Value`.
- Bind grouped properties using `@ConfigurationProperties`.
- Create and activate Spring Profiles.
- Understand configuration precedence.
- Manage secrets securely.
- Design applications that behave differently across environments.

---

# 📚 Prerequisites

- Spring Beans
- Java Configuration
- Dependency Injection
- Bean Scopes

---

# 🗺 Module Progress

```
Java Configuration

↓

@Bean

↓

@Configuration

↓

📍 Profiles & Environment

↓

Module 2 Complete
```

---

# ❓ The Real Problem

Imagine your Student Management System.

Development:

```
Database

↓

localhost
```

Testing:

```
Database

↓

test-db.company.com
```

Production:

```
Database

↓

prod-db.company.com
```

Should developers change Java code every time?

No.

Configuration should change—not code.

---

# What is Environment?

Spring's `Environment` represents the current application's configuration.

It provides access to:

- Properties
- Environment variables
- Active profiles

Think of it as a central source of configuration information.

---

# application.properties

The simplest configuration file.

```properties
server.port=8080

spring.datasource.url=jdbc:mysql://localhost:3306/studentdb

spring.datasource.username=root

spring.datasource.password=password
```

Spring automatically loads this file during startup.

---

# application.yml

The same configuration can be written using YAML.

```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/studentdb
    username: root
    password: password
```

YAML groups related properties and is often easier to read for complex configurations.

---

# Properties vs YAML

| Properties | YAML |
|------------|------|
| Flat key-value format | Hierarchical format |
| Easy for small files | Better for nested structures |
| Widely used | Increasingly popular in Spring Boot |

Spring Boot supports both formats.

---

# Reading a Property with @Value

```java
@Value("${server.port}")
private int serverPort;
```

Spring resolves the property and injects its value into the field.

---

# Problems with @Value

Suppose you have:

```properties
student.name=Rahul
student.city=Bhubaneswar
student.phone=9999999999
```

Using multiple `@Value` annotations becomes repetitive.

---

# @ConfigurationProperties

Bind related properties into one object.

```yaml
student:
  name: Rahul
  city: Bhubaneswar
  phone: 9999999999
```

```java
@ConfigurationProperties(prefix = "student")
public class StudentProperties {

    private String name;

    private String city;

    private String phone;

    // getters and setters

}
```

Now all student-related configuration lives in a single class.

---

# Why @ConfigurationProperties?

Benefits:

- Groups related settings.
- Improves readability.
- Supports validation.
- Easier to maintain.
- Strongly typed configuration.

---

# Profiles

Most applications need different settings for different environments.

Spring Profiles solve this problem.

Example:

```
Development

↓

Testing

↓

Production
```

Each profile loads different configuration.

---

# Profile Files

```
application.properties

application-dev.properties

application-test.properties

application-prod.properties
```

Only the active profile's configuration is applied in addition to the common configuration.

---

# Activating a Profile

In `application.properties`:

```properties
spring.profiles.active=dev
```

Or by environment variable or command-line argument when deploying.

---

# Profile-Specific Beans

Profiles can also control bean creation.

```java
@Service
@Profile("dev")
public class FakeEmailService {

}
```

```java
@Service
@Profile("prod")
public class AwsEmailService {

}
```

The active profile determines which implementation becomes a bean.

---

# Running Example

Development:

```
FakeEmailService
```

Production:

```
AwsEmailService
```

The application code remains unchanged.

Only configuration selects the implementation.

---

# Property Resolution Order

Spring can obtain configuration from multiple sources.

A simplified view:

```
Command Line

↓

Environment Variables

↓

application.properties

↓

Default Values
```

Higher-priority sources override lower-priority ones.

This allows deployments to customize behavior without modifying packaged configuration files.

---

# Environment API

Access properties programmatically.

```java
@Autowired
private Environment environment;

String url =
environment.getProperty(
    "spring.datasource.url"
);
```

Useful when configuration needs to be read dynamically.

---

# Secrets Management ⭐

Never store:

```
API Keys

Passwords

JWT Secrets

Database Passwords
```

directly in source code.

Prefer:

- Environment variables.
- External secret management systems.
- Secure deployment configuration.

Avoid committing sensitive information to version control.

---

# Inside Spring 🧠

Startup sequence:

```
Application Starts

↓

Load Environment

↓

Load Properties

↓

Determine Active Profile

↓

Merge Configuration

↓

Create Beans

↓

Inject Configuration

↓

Application Ready
```

Configuration is prepared before beans are initialized.

---

# Student Management System Example

Development

```
MySQL

↓

localhost

↓

FakeEmailService
```

Production

```
AWS RDS

↓

SES Email Service

↓

Real Payment Gateway
```

Same application.

Different configuration.

---

# How Spring Thinks

```
Need Property

↓

Search Property Sources

↓

Found?

↓

Inject Value

↓

Continue Startup
```

Spring resolves configuration before your business logic executes.

---

# Common Mistakes

❌ Hardcoding API keys.

❌ Committing passwords to Git.

❌ Using one configuration file for every environment.

❌ Scattering related settings across many unrelated properties.

---

# 🐞 Debugging Corner

### Problem

```text
Could not resolve placeholder
```

Cause:

Missing property.

Verify:

- Property name.
- Active profile.
- Configuration file location.

---

### Problem

Wrong bean loaded.

Cause:

Unexpected active profile.

Check:

```properties
spring.profiles.active
```

---

# 🏢 Industry Insight

Modern organizations typically maintain separate configurations for:

- Development
- Testing
- Staging
- Production

Deployment pipelines inject environment-specific values during deployment, allowing the same application artifact to be promoted across environments without recompilation.

---

# 🎤 Interview Corner

### Beginner

1. What is a Spring Profile?
2. Difference between `application.properties` and `application.yml`?
3. What is `@Value`?

---

### Intermediate

4. Why use `@ConfigurationProperties`?
5. How do profiles work?
6. What is the Environment abstraction?

---

### Advanced

7. Explain property resolution order.
8. How should secrets be managed?
9. How does Spring decide which profile-specific bean to create?

---

# 🧪 Hands-on Lab

## Objective

Run the Student Management System in multiple environments.

### Tasks

1. Create:

- `application.properties`
- `application-dev.properties`
- `application-prod.properties`

2. Configure different database URLs.

3. Create:

- `FakeEmailService`
- `AwsEmailService`

4. Use `@Profile` to activate each implementation.

5. Switch profiles.

6. Verify that:

- Different properties are loaded.
- Different beans are created.
- No Java code changes are required.

---

# 🎯 Architecture Decision

### Scenario 1

You need different payment gateways for development and production.

Would you:

- Add `if/else` statements?
- Use Spring Profiles?

Explain your choice.

---

### Scenario 2

You have 25 related payment settings.

Should you use:

- 25 `@Value` annotations
- One `@ConfigurationProperties` class

Justify your answer.

---

### Scenario 3

Where should a production database password live?

- Java source code
- `application.properties` committed to Git
- Environment variable or secret manager

Explain the safest approach.

---

# 🎯 Mini Challenge

1. What is the purpose of Spring Profiles?
2. When should `@ConfigurationProperties` be preferred over `@Value`?
3. How does Spring resolve properties?
4. Why should secrets not be committed to Git?
5. How can the same application behave differently in development and production?

---

# 📌 Module 2 Summary

In Module 2, you learned how the Spring IoC Container:

- Discovers components.
- Registers bean definitions.
- Creates and injects beans.
- Manages bean lifecycles.
- Controls bean scopes.
- Registers beans manually with Java configuration.
- Loads environment-specific configuration.
- Activates profile-specific behavior.

You now understand the complete lifecycle of a Spring-managed application—from startup to shutdown and from development to production.

---

# 📋 Final Cheat Sheet

| Topic | Key Idea |
|--------|----------|
| `application.properties` | Flat configuration |
| `application.yml` | Hierarchical configuration |
| `@Value` | Inject a single property |
| `@ConfigurationProperties` | Bind grouped properties |
| `@Profile` | Environment-specific beans |
| `Environment` | Access configuration programmatically |
| Secrets | Externalize, never hardcode |

---

# 🧠 Module 2 Final Assessment

1. Explain the complete Spring bean lifecycle.
2. What is the difference between `@Component` and `@Bean`?
3. Why is constructor injection preferred?
4. Explain singleton and prototype scopes.
5. How does component scanning work?
6. Why are stereotype annotations important?
7. What is the purpose of `@ConfigurationProperties`?
8. How do Spring Profiles work?
9. How should secrets be managed?
10. Walk through what happens after `SpringApplication.run()` is called.

---

# 🎉 What's Next?

➡ **Module 3 – Spring Boot Fundamentals**

You'll build on everything learned in Module 2 to explore:

- Spring Boot architecture
- Auto-configuration
- Starter dependencies
- Embedded Tomcat
- Spring Boot Actuator
- Externalized configuration in depth
- Spring Boot internals
- Building your first production-ready REST API
