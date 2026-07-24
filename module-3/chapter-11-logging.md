
---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 11
title: Logging – Monitoring and Troubleshooting Spring Boot Applications
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_coding_time: 35 Minutes
estimated_lab_time: 25 Minutes
version: 1.0
---

# Chapter 11
# Logging – Monitoring and Troubleshooting Spring Boot Applications

> **"Good developers write code. Great developers write logs that help solve production problems."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand the importance of logging.
- Learn how Spring Boot configures logging automatically.
- Use different logging levels effectively.
- Create logs using SLF4J.
- Configure logging using `application.properties`.
- Follow logging best practices for production applications.

---

# Introduction

Imagine your Student Management System suddenly stops working in production.

Users report that they cannot log in.

You cannot connect a debugger to the production server.

How will you identify the problem?

The answer is **Logging**.

Logs provide a record of what the application is doing, making them one of the most valuable tools for troubleshooting, monitoring, and auditing.

---

# What is Logging?

Logging is the process of recording important events that occur while an application is running.

Examples include:

- Application startup
- User login
- Database queries
- API requests
- Validation failures
- Exceptions
- System shutdown

Instead of displaying messages on the console using `System.out.println()`, enterprise applications use a logging framework.

---

# Why Logging is Important

Logging helps developers:

- Debug application issues
- Monitor production systems
- Identify performance bottlenecks
- Track user activity
- Diagnose failures
- Audit important events

Without proper logging, troubleshooting production issues becomes extremely difficult.

---

# Logging in Spring Boot

Spring Boot automatically configures logging using:

```
SLF4J

↓

Logback (Default Implementation)
```

You don't need to configure a logging framework manually.

As soon as you create a Spring Boot project, logging is ready to use.

---

# Creating a Logger

The recommended approach is to use SLF4J.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class StudentService {

    private static final Logger logger =
            LoggerFactory.getLogger(StudentService.class);

}
```

This logger can now be used throughout the class.

---

# Writing Log Messages

Example:

```java
logger.info("Student created successfully.");
```

Logging different events:

```java
logger.debug("Loading student information.");

logger.info("Student saved successfully.");

logger.warn("Student email is missing.");

logger.error("Database connection failed.");
```

Each log level has a different purpose.

---

# Logging Levels

Spring Boot supports the following logging levels.

| Level | Purpose |
|--------|----------|
| TRACE | Detailed execution information |
| DEBUG | Debugging during development |
| INFO | Normal application events |
| WARN | Potential problems |
| ERROR | Errors and exceptions |

---

# Logging Level Hierarchy

```
TRACE

↓

DEBUG

↓

INFO

↓

WARN

↓

ERROR
```

If the logging level is set to **INFO**, then:

- INFO
- WARN
- ERROR

will be displayed.

TRACE and DEBUG messages will be ignored.

---

# Configuring Logging Level

Example:

```properties
logging.level.root=INFO
```

For development:

```properties
logging.level.root=DEBUG
```

For production:

```properties
logging.level.root=INFO
```

---

# Package-Specific Logging

You can configure logging for a specific package.

Example:

```properties
logging.level.com.techvidyalaya=DEBUG
```

This enables detailed logs only for your application while keeping framework logs minimal.

---

# Logging Pattern

Spring Boot allows customization of the log output.

Example:

```properties
logging.pattern.console=%d %-5level %logger - %msg%n
```

A typical log output might look like:

```
2026-07-24  INFO  StudentService - Student created successfully.
```

---

# Logging to a File

By default, logs are printed to the console.

To save logs in a file:

```properties
logging.file.name=student-management.log
```

Or specify a directory:

```properties
logging.file.path=logs
```

Spring Boot automatically creates the log file.

---

# Logging Exceptions

Always log exceptions with the exception object.

```java
try {

    // Business Logic

} catch (Exception ex) {

    logger.error("Unable to save student.", ex);

}
```

This records the complete stack trace, making debugging much easier.

---

# Common Logging Mistakes

❌ Using `System.out.println()`.

❌ Logging sensitive information such as passwords or API keys.

❌ Logging too much information at the INFO level.

❌ Ignoring exceptions without logging them.

❌ Writing unclear log messages.

---

# Best Practices

- Use meaningful log messages.
- Choose the appropriate logging level.
- Log exceptions with stack traces.
- Avoid logging confidential data.
- Use parameterized logging instead of string concatenation.

Example:

```java
logger.info("Student {} registered successfully.", studentId);
```

This is more efficient than:

```java
logger.info("Student " + studentId + " registered successfully.");
```

---

# Industry Insight

Large enterprise applications generate millions of log entries every day.

These logs are collected using tools such as:

- ELK Stack (Elasticsearch, Logstash, Kibana)
- Splunk
- Grafana Loki
- Datadog

Operations teams analyze these logs to monitor application health, detect failures, and investigate incidents.

---

# Interview Corner

## Basic

1. What is logging?
2. Why is logging important?
3. Which logging framework does Spring Boot use by default?

---

## Intermediate

4. Explain the different logging levels.
5. How do you change the logging level?
6. How do you write logs using SLF4J?

---

## Advanced

7. Why should `System.out.println()` be avoided?
8. What is parameterized logging?
9. How would you configure different logging levels for different packages?

---

# Hands-on Lab

## Objective

Implement logging in a Spring Boot application.

### Tasks

1. Create a REST API.
2. Add logging statements using:
   - INFO
   - DEBUG
   - WARN
   - ERROR
3. Change the logging level.
4. Enable file logging.
5. Trigger an exception and observe the log output.

---

# Cheat Sheet

- Spring Boot uses **SLF4J + Logback** by default.
- Five common logging levels:
  - TRACE
  - DEBUG
  - INFO
  - WARN
  - ERROR
- Configure logging using `application.properties`.
- Use parameterized logging.
- Never log sensitive information.

---

# Summary

Logging is an essential part of every Spring Boot application. It helps developers understand application behavior, troubleshoot issues, and monitor production systems. Spring Boot simplifies logging by automatically configuring SLF4J and Logback, allowing developers to focus on writing meaningful log messages instead of configuring logging infrastructure.

---

# What's Next?

➡ **Chapter 12 – Spring Boot Actuator – Monitoring Application Health**

In the next chapter, you'll learn how Spring Boot Actuator provides production-ready endpoints for monitoring application health, metrics, environment information, and runtime behavior.
```
