
---
course: Industry Ready Java Developer
module: Module 3 - Spring Boot Fundamentals & Internal Architecture
chapter: Chapter 13
title: Spring Boot Actuator – Monitoring and Managing Applications
difficulty: Intermediate
estimated_reading_time: 50 Minutes
estimated_coding_time: 40 Minutes
estimated_lab_time: 30 Minutes
version: 1.0
---

# Chapter 13
# Spring Boot Actuator – Monitoring and Managing Applications

> **"Building an application is only half the job. The other half is knowing whether it's healthy, performing well, and ready to serve users."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand what Spring Boot Actuator is.
- Learn why monitoring is essential in production.
- Add and configure the Actuator dependency.
- Explore commonly used Actuator endpoints.
- Secure Actuator endpoints.
- Follow monitoring best practices.

---

# Introduction

Imagine your Student Management System has been deployed to production.

Suddenly, users report that the application is responding slowly.

Questions immediately arise:

- Is the application running?
- Is the database connected?
- How much memory is being used?
- How many HTTP requests are being processed?
- Is the application healthy?

Without monitoring, answering these questions becomes difficult.

Spring Boot solves this problem using **Actuator**.

---

# What is Spring Boot Actuator?

Spring Boot Actuator provides **production-ready monitoring and management features**.

It exposes endpoints that provide information about:

- Application Health
- Metrics
- Environment
- Configuration
- Beans
- Logging
- HTTP Mappings
- Application Information

These endpoints help developers and operations teams monitor applications in real time.

---

# Why Use Actuator?

Without Actuator:

```
Application Running

↓

No Visibility

↓

Guess the Problem
```

With Actuator:

```
Application Running

↓

Health Information

↓

Metrics

↓

Monitoring

↓

Quick Troubleshooting
```

Actuator improves application observability.

---

# Adding the Dependency

Add the following dependency to your project:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Restart the application.

Spring Boot automatically configures the available Actuator endpoints.

---

# Default Endpoint

The base URL is:

```
http://localhost:8080/actuator
```

This endpoint lists all exposed Actuator endpoints.

---

# Common Actuator Endpoints

| Endpoint | Purpose |
|----------|---------|
| `/actuator` | List available endpoints |
| `/actuator/health` | Application health |
| `/actuator/info` | Application information |
| `/actuator/metrics` | Performance metrics |
| `/actuator/env` | Environment properties |
| `/actuator/beans` | Registered Spring Beans |
| `/actuator/mappings` | Request mappings |
| `/actuator/loggers` | Logger configuration |
| `/actuator/threaddump` | Thread information |
| `/actuator/heapdump` | JVM heap dump |

---

# Health Endpoint

The most frequently used endpoint is:

```
GET /actuator/health
```

Typical response:

```json
{
  "status": "UP"
}
```

Possible statuses include:

- UP
- DOWN
- OUT_OF_SERVICE
- UNKNOWN

This endpoint is commonly used by load balancers and Kubernetes health checks.

---

# Metrics Endpoint

```
GET /actuator/metrics
```

Provides runtime metrics such as:

- JVM Memory Usage
- CPU Usage
- HTTP Requests
- Garbage Collection
- Active Threads

These metrics help identify performance issues.

---

# Beans Endpoint

```
GET /actuator/beans
```

Displays all Spring Beans loaded into the ApplicationContext.

Useful for:

- Debugging Bean Creation
- Verifying Auto Configuration
- Understanding the Application Context

---

# Environment Endpoint

```
GET /actuator/env
```

Displays configuration properties from:

- application.properties
- Environment Variables
- JVM Properties

Useful when troubleshooting configuration issues.

---

# Info Endpoint

You can display custom application information.

Example:

```properties
management.info.env.enabled=true

info.app.name=Student Management System

info.app.version=1.0.0

info.company=TechVidyalaya
```

Request:

```
GET /actuator/info
```

Response:

```json
{
  "app": {
    "name": "Student Management System",
    "version": "1.0.0"
  },
  "company": "TechVidyalaya"
}
```

---

# Exposing Endpoints

By default, only a few endpoints are exposed.

To expose additional endpoints:

```properties
management.endpoints.web.exposure.include=*
```

Or expose specific endpoints:

```properties
management.endpoints.web.exposure.include=health,info,metrics
```

Avoid exposing all endpoints in production unless necessary.

---

# Securing Actuator

Some endpoints reveal sensitive information.

For production:

- Expose only required endpoints.
- Secure endpoints using Spring Security.
- Restrict access to administrators.
- Disable endpoints that are not required.

---

# Monitoring Flow

```
Application

↓

Spring Boot Actuator

↓

Health

↓

Metrics

↓

Monitoring Tool

↓

Operations Dashboard
```

Actuator acts as a bridge between your application and monitoring systems.

---

# Industry Integration

Actuator integrates with:

- Prometheus
- Grafana
- Micrometer
- Kubernetes
- Docker
- Spring Boot Admin

These tools provide dashboards, alerts, and real-time monitoring.

---

# Common Mistakes

❌ Exposing every Actuator endpoint in production.

❌ Leaving sensitive endpoints unsecured.

❌ Ignoring application health checks.

❌ Never monitoring JVM memory usage.

---

# Best Practices

- Always enable the Health endpoint.
- Expose only necessary endpoints.
- Protect Actuator with authentication.
- Monitor JVM memory and HTTP metrics.
- Integrate with monitoring platforms such as Prometheus and Grafana.

---

# Industry Insight

In enterprise systems, monitoring is just as important as development.

Operations teams use Actuator to:

- Detect failures
- Monitor resource usage
- Investigate incidents
- Scale applications
- Trigger alerts automatically

Actuator is a core component of modern DevOps and cloud-native applications.

---

# Interview Corner

## Basic

1. What is Spring Boot Actuator?
2. Why is Actuator used?
3. Which dependency enables Actuator?

---

## Intermediate

4. Explain the Health endpoint.
5. What information does the Metrics endpoint provide?
6. How do you expose additional Actuator endpoints?

---

## Advanced

7. Why shouldn't all Actuator endpoints be exposed in production?
8. How does Actuator integrate with Prometheus?
9. How would you secure Actuator endpoints?

---

# Hands-on Lab

## Objective

Monitor a Spring Boot application using Actuator.

### Tasks

1. Add the Actuator dependency.
2. Run the application.
3. Visit:
   - `/actuator`
   - `/actuator/health`
   - `/actuator/info`
   - `/actuator/metrics`
4. Configure custom application information.
5. Expose selected endpoints.
6. Observe application metrics during API requests.

---

# Cheat Sheet

- Add `spring-boot-starter-actuator` to enable monitoring.
- Base URL: `/actuator`
- Health endpoint: `/actuator/health`
- Metrics endpoint: `/actuator/metrics`
- Expose only required endpoints.
- Secure Actuator in production.

---

# Summary

Spring Boot Actuator provides production-ready monitoring and management capabilities for Spring Boot applications. It exposes health information, runtime metrics, environment details, and other operational data that help developers and operations teams monitor, troubleshoot, and maintain applications efficiently. Combined with monitoring platforms such as Prometheus and Grafana, Actuator plays a vital role in building reliable enterprise applications.

---

# What's Next?

➡ **Chapter 14 – Spring Boot DevTools – Improving Developer Productivity**

In the next chapter, you'll learn how Spring Boot DevTools speeds up development through automatic restarts, LiveReload support, and other features that improve the developer experience.
