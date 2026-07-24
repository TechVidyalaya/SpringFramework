---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 8
title: Embedded Web Servers – Running Applications Without External Tomcat
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_coding_time: 30 Minutes
estimated_lab_time: 20 Minutes
version: 1.0
---

# Chapter 8
# Embedded Web Servers – Running Applications Without External Tomcat

> **"Spring Boot changed Java web development by packaging the web server inside the application, making deployment as simple as running a JAR file."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what an embedded web server is.
- Compare traditional deployment with Spring Boot deployment.
- Learn how Tomcat is embedded into a Spring Boot application.
- Understand the request lifecycle from browser to controller.
- Configure server settings such as port and context path.
- Switch between Tomcat, Jetty, and Undertow.

---

# Introduction

Before Spring Boot, deploying a Java web application involved several manual steps:

- Build a WAR file
- Install Tomcat or another application server
- Copy the WAR file into the server
- Start the server
- Verify deployment

Spring Boot simplified this process by embedding the web server inside the application itself.

Now, running a web application is often as simple as:

```bash
java -jar student-management.jar
```

---

# What is an Embedded Web Server?

An embedded web server is a web server packaged as part of your application.

Instead of installing Tomcat separately, Spring Boot includes it inside the application.

Common embedded servers supported by Spring Boot:

- Tomcat (Default)
- Jetty
- Undertow

---

# Traditional Deployment

```
Application

↓

WAR File

↓

External Tomcat

↓

Deploy

↓

Start Server

↓

Application Ready
```

---

# Spring Boot Deployment

```
Application

↓

Executable JAR

↓

Embedded Tomcat

↓

Run

↓

Application Ready
```

No external server installation is required.

---

# How Embedded Tomcat Starts

When `SpringApplication.run()` executes:

```
SpringApplication.run()

↓

Create ApplicationContext

↓

Auto Configuration

↓

Create Tomcat Server

↓

Bind Port 8080

↓

Start HTTP Listener

↓

Application Ready
```

The embedded server starts automatically before the application begins accepting requests.

---

# Request Flow

When a user opens:

```
http://localhost:8080/students
```

the request travels through the following path:

```
Browser

↓

Embedded Tomcat

↓

DispatcherServlet

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Response

↓

Browser
```

Tomcat receives the HTTP request, while Spring MVC processes it.

---

# Default Server

When using:

```xml
spring-boot-starter-web
```

Spring Boot automatically includes **Apache Tomcat**.

You don't need to install or configure it separately.

---

# Changing the Server Port

By default:

```
8080
```

To use another port:

```properties
server.port=9090
```

Now the application starts on:

```
http://localhost:9090
```

---

# Changing the Context Path

Example:

```properties
server.servlet.context-path=/student-api
```

Application URL becomes:

```
http://localhost:8080/student-api
```

This is useful when hosting multiple applications on the same server.

---

# Switching to Jetty

Remove the default Tomcat starter and add:

```xml
spring-boot-starter-jetty
```

Spring Boot automatically starts Jetty instead of Tomcat.

---

# Switching to Undertow

Similarly, replace Tomcat with:

```xml
spring-boot-starter-undertow
```

No code changes are required.

---

# Tomcat vs Jetty vs Undertow

| Server | Best For |
|---------|----------|
| Tomcat | General-purpose web applications |
| Jetty | Lightweight web services |
| Undertow | High-performance applications |

Tomcat remains the most commonly used option in enterprise projects.

---

# Common Server Configuration

| Property | Description |
|----------|-------------|
| server.port | Server port |
| server.address | Bind IP address |
| server.servlet.context-path | Base URL path |
| server.shutdown | Graceful shutdown |
| server.error.whitelabel.enabled | Enable/Disable default error page |

---

# Benefits of Embedded Servers

- No external installation
- Faster deployment
- Easier development
- Same environment in development and production
- Portable executable JAR

---

# Common Mistakes

❌ Port already in use.

❌ Assuming Tomcat must be installed separately.

❌ Forgetting to update URLs after changing the context path.

❌ Including multiple embedded server dependencies.

---

# Best Practices

- Use the default Tomcat unless there is a specific requirement.
- Keep server configuration in `application.properties`.
- Use configurable ports for different environments.
- Enable graceful shutdown in production.

---

# Industry Insight

Most modern Spring Boot microservices are packaged as executable JAR files with embedded Tomcat.

This simplifies:

- Docker containerization
- Kubernetes deployment
- CI/CD pipelines
- Cloud deployments

Instead of managing application servers, teams simply deploy the application JAR or container image.

---

# Interview Corner

### Basic

1. What is an embedded web server?
2. Which server is the default in Spring Boot?
3. How do you change the server port?

### Intermediate

4. Explain the difference between WAR and executable JAR.
5. What is the purpose of the context path?
6. How do you replace Tomcat with Jetty?

### Advanced

7. Why are embedded servers preferred in microservices?
8. Compare Tomcat, Jetty, and Undertow.
9. Explain the request flow from browser to controller.

---

# Hands-on Lab

## Objective

Configure and run a Spring Boot application with an embedded server.

### Tasks

1. Create a Spring Boot Web project.
2. Run the application.
3. Observe Tomcat startup logs.
4. Change the server port to `9090`.
5. Add a custom context path.
6. Replace Tomcat with Jetty and verify the application still runs.

---

# Cheat Sheet

- Spring Boot uses Tomcat by default.
- Embedded servers eliminate manual deployment.
- Applications run as executable JAR files.
- Server settings are configured in `application.properties`.
- Jetty and Undertow can replace Tomcat without changing application code.

---

# Summary

Embedded web servers are one of Spring Boot's most significant innovations. By packaging the web server within the application, Spring Boot removes the need for external application server installation and simplifies deployment. Whether running locally, inside Docker, or on cloud platforms, the same executable application can be deployed consistently across environments.

---

# What's Next?

➡ **Chapter 9 – Configuration Files – Managing Application Settings**

In the next chapter, you'll learn how Spring Boot uses `application.properties` and `application.yml` to manage application configuration, making it easy to customize behavior without changing code.
