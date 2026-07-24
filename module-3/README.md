---
course: Industry Ready Java Developer
module: Module 2 - Spring Core (IoC, Dependency Injection & Bean Management)
version: 1.0
author: TechVidyalaya
difficulty: Intermediate
estimated_duration: 25-35 Hours
prerequisites:
  - Java Programming
  - Object-Oriented Programming
  - Maven Basics
  - Module 1 - Introduction to Spring Framework
---

# Module 2
# Spring Core (IoC, Dependency Injection & Bean Management)

> **"Spring Core is the heart of the Spring Framework. Everything else in Spring—Spring Boot, Spring MVC, Spring Security, Spring Data JPA, Spring AI, and Spring Cloud—is built on top of the concepts you'll learn in this module."**

---

# Welcome

Congratulations on completing Module 1!

You now understand:

- Why Spring was created
- Enterprise Java problems
- Spring ecosystem
- Spring architecture
- Project setup

In this module, you'll finally learn **how Spring actually works internally**.

This is the most important module of the entire Spring course because it builds the mental model required to understand every other Spring technology.

---

# Why This Module Matters

Imagine building an enterprise application.

Instead of manually creating every object:

```java
StudentRepository repository =
        new StudentRepository();

StudentService service =
        new StudentService(repository);

StudentController controller =
        new StudentController(service);
```

Spring does all of this automatically.

But...

How?

This module answers that question.

---

# What You'll Learn

By the end of this module, you'll understand:

- What the Spring IoC Container is
- How Spring discovers classes
- How beans are created
- How dependencies are injected
- How object lifecycles work
- How bean scopes work
- How configuration classes work
- How environments and profiles work

Most importantly...

You'll understand **what happens internally after calling:**

```java
SpringApplication.run(...)
```

---

# Learning Philosophy

This module is intentionally different from most online tutorials.

Instead of teaching only annotations, you'll learn:

- Why the feature exists
- What problem it solves
- How Spring implements it internally
- How memory changes
- How execution flows
- Where it is used in real projects
- Common production mistakes
- Debugging strategies
- Interview expectations

The goal is to help you think like a Spring Framework engineer—not just a Spring user.

---

# Running Project

Throughout this module, we will build a single running example.

```
Student Management System
```

Instead of using unrelated examples in every chapter, we'll continue improving one enterprise application.

Packages:

```
com.techvidyalaya.student

├── controller
├── service
├── repository
├── entity
├── config
└── application
```

Classes:

```
StudentController

↓

StudentService

↓

StudentRepository

↓

Student
```

Every chapter extends this project.

---

# Module Roadmap

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

Beans

↓

Bean Lifecycle

↓

Dependency Injection

↓

Constructor Injection

↓

Setter Injection

↓

Component Scanning

↓

Stereotype Annotations

↓

Bean Scopes

↓

Java Configuration

↓

Profiles & Environment
```

By the end of the module you'll understand the complete bean lifecycle.

---

# Chapter Overview

## Chapter 1

### Spring Core Architecture

Learn:

- What Spring Core is
- Spring Container
- IoC
- Architecture
- Bean Definitions

---

## Chapter 2

### Inversion of Control (IoC)

Learn:

- Traditional Java
- Tight Coupling
- IoC
- Control inversion
- Real-world analogy

---

## Chapter 3

### Spring IoC Container

Learn:

- Bean creation
- Bean registry
- Startup flow
- ApplicationContext creation

---

## Chapter 4

### BeanFactory

Learn:

- Lazy initialization
- getBean()
- Memory usage
- Performance

---

## Chapter 5

### ApplicationContext

Learn:

- Enterprise container
- Events
- Resource loading
- Environment
- MessageSource

---

## Chapter 6

### Spring Beans

Learn:

- What is a Bean?
- Bean registration
- Bean naming
- Bean metadata

---

## Chapter 7

### Bean Lifecycle

Learn:

- Bean creation
- Dependency injection
- Initialization
- PostConstruct
- PreDestroy
- BeanPostProcessor

---

## Chapter 8

### Dependency Injection

Learn:

- Constructor Injection
- Setter Injection
- Field Injection
- Loose coupling

---

## Chapter 9

### Constructor Injection

Learn:

- Best practices
- Immutability
- Circular dependency
- Testing
- Optional dependencies

---

## Chapter 10

### Setter Injection & Field Injection

Learn:

- Optional dependencies
- Reflection
- Field injection drawbacks
- Design review

---

## Chapter 11

### Component Scanning

Learn:

- @ComponentScan
- Package scanning
- BeanDefinition creation
- Internal scanners

---

## Chapter 12

### Stereotype Annotations

Learn:

- @Component
- @Service
- @Repository
- @Controller
- @RestController
- @Configuration

---

## Chapter 13

### Bean Scopes

Learn:

- Singleton
- Prototype
- Request
- Session
- Application
- WebSocket

---

## Chapter 14

### Java Configuration

Learn:

- @Configuration
- @Bean
- Third-party libraries
- Full vs Lite configuration
- proxyBeanMethods

---

## Chapter 15

### Profiles & Environment

Learn:

- application.properties
- YAML
- @Value
- @ConfigurationProperties
- Profiles
- Secrets
- Environment

---

# Teaching Approach

Every chapter follows the same structure.

```
Learning Objectives

↓

Industry Story

↓

Problem Statement

↓

Why It Exists

↓

Architecture

↓

Internal Working

↓

Memory Representation

↓

Execution Flow

↓

Code Example

↓

Behind the Scenes

↓

Industry Insight

↓

Debugging Corner

↓

Interview Questions

↓

Hands-on Lab

↓

Mini Challenge

↓

Cheat Sheet

↓

Quiz

↓

Summary
```

This consistency makes learning easier and helps reinforce concepts.

---

# Skills You'll Gain

After completing this module, you'll be able to:

✅ Explain Spring Core Architecture

✅ Explain IoC

✅ Explain Dependency Injection

✅ Build loosely coupled applications

✅ Design Spring Beans

✅ Configure Spring applications

✅ Debug bean creation problems

✅ Use different bean scopes

✅ Manage environments

✅ Configure production-ready applications

---

# Industry Relevance

Every enterprise Spring Boot application relies on these concepts.

Examples include:

- Banking Applications
- E-Commerce Platforms
- Healthcare Systems
- ERP Applications
- AI Platforms
- SaaS Products
- Government Portals

If you master this module, you'll understand the foundation of nearly every Spring Boot project you'll encounter in your career.

---

# Recommended Learning Order

For the best understanding:

1. Read the chapter.
2. Study every diagram.
3. Type the code yourself.
4. Complete the lab.
5. Answer the interview questions.
6. Finish the mini challenge.
7. Review the cheat sheet.
8. Attempt the quiz.
9. Build the running project.

Avoid skipping chapters, as each one builds directly on the previous concepts.

---

# Expected Outcomes

After completing Module 2, you will no longer see Spring as a "magic framework."

Instead, you'll understand:

- how it discovers components,
- how it creates beans,
- how dependencies are resolved,
- how scopes affect object lifetime,
- how configuration is processed,
- and how the application becomes ready to serve requests.

These concepts form the foundation for everything you'll learn in Spring Boot, Spring MVC, Spring Data JPA, Spring Security, Spring Cloud, and beyond.

---

# Next Module

➡ **Module 3 – Spring Boot Fundamentals**

You'll explore:

- Spring Boot Architecture
- Auto Configuration
- Starter Dependencies
- Embedded Tomcat
- Spring Boot Internals
- Spring Boot Actuator
- Building Production REST APIs

---

> **"Once you understand Spring Core, you're no longer memorizing annotations—you understand the engine that powers the entire Spring ecosystem."**
