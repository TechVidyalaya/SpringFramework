---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
chapter: Chapter 14
title: Java Configuration & @Bean - Registering Beans Manually
difficulty: Intermediate
estimated_time: 240 Minutes
version: 1.0
---

# Chapter 14
# Java Configuration & @Bean – Registering Beans Manually

> **"Not every bean is discovered. Some beans are deliberately created and registered by you."**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain Java-based configuration.
- Understand `@Configuration`.
- Create beans using `@Bean`.
- Register third-party library classes.
- Compare `@Component` and `@Bean`.
- Understand full vs lite configuration.
- Explain configuration class enhancement.

---

# 📚 Prerequisites

- Spring Beans
- Component Scanning
- Stereotype Annotations
- Bean Scopes

---

# 🗺 Module Progress

```
Component Scanning

↓

Stereotypes

↓

Bean Scopes

↓

📍 Java Configuration

↓

Profiles & Environment
```

---

# ❓ Why Do We Need @Bean?

Consider this class from the Jackson library.

```java
ObjectMapper
```

Can we do this?

```java
@Component
public class ObjectMapper {

}
```

No.

The source code belongs to a third-party library.

We cannot modify it.

Yet we still want Spring to manage it.

---

# Java Configuration

Spring allows us to register beans explicitly.

```java
@Configuration
public class AppConfig {

}
```

Think of a configuration class as a factory that creates Spring-managed objects.

---

# First @Bean

```java
@Configuration
public class AppConfig {

    @Bean
    public ObjectMapper objectMapper(){

        return new ObjectMapper();

    }

}
```

The returned object becomes a Spring Bean.

---

# Startup Flow

```
Application Starts

↓

Component Scan

↓

Find AppConfig

↓

@Configuration

↓

Invoke @Bean Method

↓

Object Created

↓

BeanDefinition Registered

↓

ApplicationContext
```

Notice that the object returned by the method becomes part of the container.

---

# @Bean vs @Component

Both create Spring-managed beans.

The difference is *how* they are registered.

| Feature | `@Component` | `@Bean` |
|---------|--------------|----------|
| Bean Source | Your class | Returned by a method |
| Requires Source Code | Yes | No |
| Third-party Libraries | No | Yes |
| Registration | Automatic | Explicit |

---

# Typical Use Cases

`@Component`

- StudentService
- StudentRepository
- StudentController

`@Bean`

- ObjectMapper
- RestTemplate
- ModelMapper
- PasswordEncoder
- AWS SDK Client
- Kafka Producer

---

# How Spring Thinks

```
Found AppConfig

↓

@Configuration?

↓

Yes

↓

Find @Bean Methods

↓

Invoke Method

↓

Receive Object

↓

Register Bean
```

The container treats the returned object like any other Spring-managed bean.

---

# Bean Dependencies

A `@Bean` method can also depend on other beans.

```java
@Bean
public StudentService studentService(
        StudentRepository repository){

    return new StudentService(repository);

}
```

Spring resolves the `StudentRepository` parameter exactly as it would for constructor injection.

---

# Configuration Classes

Configuration classes are themselves Spring beans.

```
ApplicationContext

├── StudentService

├── StudentRepository

├── AppConfig
```

Spring manages the configuration class before invoking its `@Bean` methods.

---

# Full vs Lite Configuration ⭐

This is a topic many courses skip.

## Full Configuration

```java
@Configuration
public class AppConfig {

}
```

Spring enhances the configuration class to ensure that repeated calls between `@Bean` methods still return the managed singleton bean rather than creating new objects.

---

## Lite Configuration

```java
@Component
public class AppConfig {

    @Bean

}
```

The `@Bean` methods are still recognized.

However, the class is **not** treated as a full configuration class, so calls between `@Bean` methods behave like normal Java method calls.

This distinction matters when one `@Bean` method directly invokes another.

---

# Example

```java
@Bean
public StudentRepository repository(){

    return new StudentRepository();

}

@Bean
public StudentService service(){

    return new StudentService(repository());

}
```

With a full `@Configuration` class, Spring ensures that `repository()` returns the managed singleton bean.

Without full configuration, that direct method call can create a new object.

---

# proxyBeanMethods

In recent Spring versions:

```java
@Configuration(proxyBeanMethods = false)
```

disables method interception.

Use it when:

- `@Bean` methods are independent.
- You don't call one `@Bean` method from another.
- Faster startup and lower overhead are desirable.

Leave the default behavior when inter-bean method calls are required.

---

# Inside Spring 🧠

```
@Configuration

↓

ConfigurationClassPostProcessor

↓

Parse @Bean Methods

↓

BeanDefinition

↓

DefaultListableBeanFactory

↓

Instantiate Bean
```

`ConfigurationClassPostProcessor` is responsible for processing configuration classes and registering their bean definitions.

---

# Third-Party Library Example

```java
@Bean
public RestTemplate restTemplate(){

    return new RestTemplate();

}
```

Later:

```java
@Service
public class StudentService {

    private final RestTemplate restTemplate;

    public StudentService(RestTemplate restTemplate){

        this.restTemplate = restTemplate;

    }

}
```

Spring injects the `RestTemplate` just like any other bean.

---

# Common Mistakes

❌ Creating third-party objects using `new` throughout the application.

Instead, register one managed bean and inject it where needed.

---

❌ Using `@Component` for classes you do not own.

If you cannot modify the source code, use a configuration class with a `@Bean` method.

---

❌ Misunderstanding `proxyBeanMethods`.

Only disable proxying after understanding how your `@Bean` methods interact.

---

# 🐞 Debugging Corner

### Problem

```text
NoSuchBeanDefinitionException
```

Cause:

The configuration class was not scanned or the `@Bean` method was never processed.

---

### Problem

Unexpected duplicate objects.

Cause:

Direct calls between `@Bean` methods inside a lite configuration class.

---

# 🏢 Industry Insight

Enterprise projects commonly use Java configuration to register infrastructure components such as:

- HTTP clients
- Database connection pools
- Message brokers
- Object mappers
- Authentication providers
- External SDK clients

Keeping these definitions in configuration classes centralizes infrastructure management and simplifies testing.

---

# 🎤 Interview Corner

### Beginner

1. What is `@Bean`?
2. What is `@Configuration`?
3. Why can't we annotate third-party classes with `@Component`?

---

### Intermediate

4. Difference between `@Bean` and `@Component`?
5. When should Java configuration be used?
6. How are dependencies injected into `@Bean` methods?

---

### Advanced

7. Explain full vs lite configuration.
8. What does `proxyBeanMethods` do?
9. What is the role of `ConfigurationClassPostProcessor`?

---

# 🧪 Hands-on Lab

## Objective

Register third-party library classes.

### Tasks

1. Create an `AppConfig` class.

2. Register:

- `ObjectMapper`
- `RestTemplate`
- `ModelMapper`

using `@Bean`.

3. Inject each bean into `StudentService`.

4. Verify they are managed by Spring.

5. Experiment with:

```java
@Configuration(proxyBeanMethods = false)
```

Observe how your configuration behaves when one `@Bean` method calls another.

---

# 🎯 Architecture Decision

### Scenario 1

```
StudentService
```

Should it use:

- `@Service`
- `@Bean`

Explain why.

---

### Scenario 2

```
ObjectMapper
```

Should it use:

- `@Component`
- `@Bean`

Explain why.

---

### Scenario 3

```
AWS S3 Client
```

Which registration mechanism is more appropriate?

Justify your answer.

---

# 🎯 Mini Challenge

1. Why do we need `@Bean`?
2. What is the purpose of `@Configuration`?
3. When should `@Bean` be preferred over `@Component`?
4. What is full configuration?
5. What does `proxyBeanMethods` control?

---

# 📌 Chapter Summary

Java configuration provides a manual bean registration mechanism for cases where component scanning is not appropriate. Using `@Configuration` and `@Bean`, developers can register application-specific components as well as third-party library objects. Understanding full configuration, lite configuration, and `proxyBeanMethods` helps explain how Spring manages configuration classes internally while preserving singleton behavior.

---

# 📋 Cheat Sheet

| Concept | Description |
|---------|-------------|
| `@Configuration` | Defines a configuration class |
| `@Bean` | Registers a bean returned by a method |
| Automatic Registration | `@Component` + component scanning |
| Manual Registration | `@Bean` methods |
| Typical Use | Third-party libraries and infrastructure |

---

# 🧠 Quiz

1. What is Java configuration?
2. Why do we need `@Bean`?
3. How is `@Bean` different from `@Component`?
4. What is full configuration?
5. What does `proxyBeanMethods` do?
6. Why is Java configuration useful for third-party libraries?

---

# 📖 What's Next?

➡ **Chapter 15 – Profiles & Environment: Building Applications for Development, Testing, and Production**

You'll learn how Spring externalizes configuration using properties and YAML files, how `Environment`, `@Value`, and `@ConfigurationProperties` work, how to activate different profiles, and how to build applications that behave differently in development, testing, and production without changing code.
