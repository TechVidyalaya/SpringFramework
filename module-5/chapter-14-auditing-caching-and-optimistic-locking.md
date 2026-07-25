---
title: Auditing, Caching, and Optimistic Locking
module: Module 5 – Spring Data JPA & Hibernate
chapter: 14
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Advanced
estimated_reading_time: 60 Minutes
estimated_practical_time: 90 Minutes
---

# Chapter 14
# Auditing, Caching, and Optimistic Locking

> **"Enterprise applications must answer three important questions: Who changed the data? How can we make data retrieval faster? What happens if two users update the same record at the same time?"**

---

# 📖 Introduction

Imagine a Student Management System used by hundreds of staff members.

One administrator updates a student's department.

At the same time, another administrator changes the student's email address.

Meanwhile, thousands of users are viewing student records.

Three important challenges arise:

- **Who modified the student record?**
- **How can repeated database queries be avoided?**
- **How do we prevent one update from overwriting another?**

Spring Data JPA provides solutions through:

- **Auditing**
- **Caching**
- **Optimistic Locking**

These features are commonly used in enterprise applications to improve traceability, performance, and data consistency.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Auditing
- Enable Spring Data JPA Auditing
- Automatically track entity changes
- Understand JPA caching
- Learn First-Level and Second-Level Cache
- Implement Optimistic Locking
- Handle concurrent updates
- Follow enterprise best practices

---

# Part 1 – Auditing

---

# What is Auditing?

Auditing automatically records information about database changes.

Typical audit information includes:

- Who created the record
- When it was created
- Who last updated it
- When it was updated

Example

```text
Student

----------------------------

Name

Department

Created By

Created Date

Last Modified By

Last Modified Date

----------------------------
```

---

# Why Auditing?

Without auditing:

```text
Student Updated

↓

Unknown
```

With auditing:

```text
Student Updated

↓

Admin

↓

25 July 2026

↓

10:45 AM
```

Auditing provides accountability and simplifies troubleshooting.

---

# Enable Auditing

Enable auditing in the Spring Boot application.

```java
@SpringBootApplication
@EnableJpaAuditing
public class StudentApplication {

}
```

---

# Auditing Annotations

Spring Data JPA provides the following annotations:

| Annotation | Purpose |
|------------|---------|
| `@CreatedDate` | Creation timestamp |
| `@LastModifiedDate` | Last update timestamp |
| `@CreatedBy` | Creator |
| `@LastModifiedBy` | Last modifier |

---

# Entity Listener

Enable auditing for an entity.

```java
@Entity
@EntityListeners(
AuditingEntityListener.class
)
public class Student {

}
```

---

# Created Date

```java
@CreatedDate
private LocalDateTime createdAt;
```

Automatically populated when the entity is first saved.

---

# Last Modified Date

```java
@LastModifiedDate
private LocalDateTime updatedAt;
```

Automatically updated whenever the entity changes.

---

# Created By

```java
@CreatedBy
private String createdBy;
```

Typically stores the logged-in user's username.

---

# Last Modified By

```java
@LastModifiedBy
private String updatedBy;
```

Stores the user who most recently modified the record.

---

# Audit Flow

```text
Save Entity

↓

Spring Auditing

↓

Populate Audit Fields

↓

Database
```

---

# AuditorAware

Spring needs to know the current user.

```java
@Component
public class AuditorAwareImpl
implements AuditorAware<String> {

    @Override
    public Optional<String>
    getCurrentAuditor() {

        return Optional.of("admin");

    }

}
```

In production, this value usually comes from Spring Security.

---

# Part 2 – Caching

---

# What is Caching?

A cache stores frequently accessed data in memory.

Instead of reading the database repeatedly,

the application retrieves data from the cache.

---

# Without Cache

```text
Request

↓

Database

↓

Response

↓

Request

↓

Database

↓

Response
```

Every request hits the database.

---

# With Cache

```text
First Request

↓

Database

↓

Cache

↓

Second Request

↓

Cache

↓

Response
```

The database is accessed only when necessary.

---

# Benefits of Caching

- Faster response times
- Reduced database load
- Better scalability
- Improved user experience

---

# First-Level Cache

Hibernate automatically provides a **First-Level Cache**.

Characteristics:

- Enabled by default
- Scoped to a single `EntityManager` (or Hibernate `Session`)
- No additional configuration required

Example

```java
Student student1 =
entityManager.find(
Student.class, 1L);

Student student2 =
entityManager.find(
Student.class, 1L);
```

Only the first call executes SQL.

The second call retrieves the entity from the first-level cache.

---

# First-Level Cache Flow

```text
EntityManager

↓

Cache

↓

Database
```

---

# Second-Level Cache

Unlike the first-level cache,

the second-level cache is shared across multiple sessions.

Popular providers include:

- Ehcache
- Caffeine
- Hazelcast
- Infinispan

Example

```text
Application

↓

Second-Level Cache

↓

Database
```

This significantly reduces repeated database access.

---

# Enabling Second-Level Cache

Example configuration:

```properties
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
```

A cache provider must also be configured.

---

# Cache Example

First request

```text
Student ID = 1

↓

Database
```

Second request

```text
Student ID = 1

↓

Cache
```

No SQL is executed.

---

# When to Use Cache

Suitable for:

- Reference data
- Product catalogues
- Countries
- Departments
- Course lists
- Frequently viewed records

Avoid caching data that changes very frequently.

---

# Part 3 – Optimistic Locking

---

# Why Do We Need Locking?

Suppose two administrators edit the same student.

```text
Admin A

↓

Student

↑

Admin B
```

Both load the same record.

---

# Problem

Database

```text
Student

Department

Computer Science
```

Admin A changes

```text
Information Technology
```

Admin B changes

```text
Artificial Intelligence
```

Without locking,

the last update overwrites the first one.

---

# Lost Update Problem

```text
Admin A

↓

Save

↓

Database Updated

↓

Admin B

↓

Save

↓

Admin A's Changes Lost
```

---

# Optimistic Locking

Optimistic Locking assumes conflicts are rare.

Instead of locking the record,

Hibernate checks whether the record has changed before updating it.

---

# @Version Annotation

Add a version field.

```java
@Version
private Long version;
```

Hibernate automatically manages this value.

---

# Example

Initial record

```text
Version = 1
```

Admin A

```text
Update

↓

Version = 2
```

Admin B still has

```text
Version = 1
```

When Admin B tries to save,

Hibernate throws:

```text
OptimisticLockException
```

This prevents accidental overwrites.

---

# Generated SQL

```sql
UPDATE students
SET
name = ?,
version = version + 1
WHERE
id = ?
AND version = ?;
```

If no rows are updated,

Hibernate detects a concurrent modification.

---

# Handling the Exception

```java
try {

    repository.save(student);

}
catch (
OptimisticLockException ex) {

    // Inform user
    // Reload latest data

}
```

Users can then review the latest version before saving again.

---

# Optimistic vs Pessimistic Locking

| Optimistic | Pessimistic |
|------------|-------------|
| No database lock | Database row is locked |
| Better performance | More locking overhead |
| Suitable for most applications | Suitable for high-contention systems |
| Uses `@Version` | Uses explicit database locks |

---

# Which Locking Strategy Should You Choose?

Use **Optimistic Locking** when:

- Conflicts are rare
- Read operations are more common than writes
- High concurrency is expected

Use **Pessimistic Locking** when:

- Conflicts occur frequently
- Financial transactions require strict locking
- Multiple users edit the same records simultaneously

---

# Enterprise Flow

```text
User Request

↓

Service

↓

@Transactional

↓

Hibernate

↓

Auditing

↓

Optimistic Lock Check

↓

Cache

↓

Database
```

---

# Best Practices

- Enable auditing for important business entities.
- Cache frequently accessed reference data.
- Use `@Version` for entities updated by multiple users.
- Handle `OptimisticLockException` gracefully.
- Monitor cache hit and miss rates in production.

---

# Common Mistakes

❌ Forgetting `@EntityListeners` when enabling auditing.

❌ Caching rapidly changing data.

❌ Ignoring optimistic locking in multi-user applications.

❌ Catching and silently ignoring `OptimisticLockException`.

❌ Assuming the first-level cache is shared across sessions.

---

# Industry Insight

These three features are found in almost every enterprise system.

| Feature | Example |
|---------|----------|
| Auditing | Banking transaction history |
| Caching | Product catalogues in e-commerce |
| Optimistic Locking | CRM systems, ERP software, HR portals |

Together they improve:

- Performance
- Reliability
- Data integrity
- User experience

---

# 🧪 Hands-on Lab

## Objective

Enhance the Student Management System with enterprise features.

### Tasks

1. Enable JPA auditing.
2. Add audit fields to the `Student` entity.
3. Implement `AuditorAware`.
4. Add a `@Version` field.
5. Simulate two concurrent updates.
6. Observe `OptimisticLockException`.
7. Configure a second-level cache provider.
8. Compare database queries before and after caching.

---

# 💼 Interview Corner

### Q1. What is JPA Auditing?

JPA Auditing automatically records information such as who created or modified an entity and when those actions occurred.

---

### Q2. What is the difference between the first-level and second-level cache?

The first-level cache is associated with a single `EntityManager`, while the second-level cache is shared across multiple sessions and application requests.

---

### Q3. What is the purpose of the `@Version` annotation?

It enables optimistic locking by maintaining a version number for each entity and detecting concurrent modifications.

---

### Q4. What problem does optimistic locking solve?

It prevents the **Lost Update Problem**, where one user's changes unintentionally overwrite another user's changes.

---

### Q5. When should optimistic locking be preferred?

It is preferred in applications with many reads and relatively few concurrent updates because it provides good performance without locking database rows.

---

# 📄 Cheat Sheet

| Feature | Purpose |
|---------|---------|
| `@EnableJpaAuditing` | Enable auditing |
| `@CreatedDate` | Store creation timestamp |
| `@LastModifiedDate` | Store last update timestamp |
| `@CreatedBy` | Store creator |
| `@LastModifiedBy` | Store last modifier |
| `AuditorAware` | Provide current user |
| First-Level Cache | Session-level cache |
| Second-Level Cache | Shared application cache |
| `@Version` | Enable optimistic locking |
| `OptimisticLockException` | Detect concurrent updates |

---

# 📝 Chapter Summary

In this chapter, you learned how to build more reliable and scalable applications using three important enterprise features. You enabled **JPA Auditing** to automatically track entity changes, explored **Hibernate Caching** to improve application performance, and implemented **Optimistic Locking** with the `@Version` annotation to prevent concurrent update conflicts.

These capabilities are essential for enterprise systems where performance, traceability, and data consistency are critical.

---

# 🚀 What's Next?

In **Chapter 15 – Building a Production-Ready Database Application**, you'll combine everything learned throughout this module to build a complete enterprise-grade Student Management System with layered architecture, validation, transactions, relationships, auditing, pagination, dynamic searching, caching, and production-ready best practices.
