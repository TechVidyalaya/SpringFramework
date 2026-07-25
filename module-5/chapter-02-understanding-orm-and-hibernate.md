---
title: Understanding ORM and Hibernate
module: Module 5 – Spring Data JPA & Hibernate
chapter: 2
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Beginner
estimated_reading_time: 55 Minutes
estimated_practical_time: 60 Minutes
prerequisites:
  - Introduction to Spring Data JPA
---

# Chapter 2
# Understanding ORM and Hibernate

> **"Databases understand tables. Java understands objects. ORM is the translator that allows them to communicate seamlessly."**

---

# 📖 Introduction

In the previous chapter, we learned why enterprise applications require databases and how Spring Data JPA simplifies database development.

However, an important question still remains.

**How does a Java object become a database row?**

Consider the following Java object:

```java
Student student = new Student();

student.setName("Rahul");
student.setEmail("rahul@gmail.com");
```

A relational database has no idea what a Java object is.

Instead, it understands something like this:

| id | name | email |
|----|------|--------|
| 1 | Rahul | rahul@gmail.com |

Some mechanism must convert Java objects into database records and vice versa.

That mechanism is called **Object Relational Mapping (ORM)**.

Hibernate is the most popular ORM framework in the Java ecosystem and is the default implementation used by Spring Data JPA.

In this chapter, you'll understand how Hibernate performs this conversion and how it manages the lifecycle of entities behind the scenes.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain Object Relational Mapping (ORM)
- Understand why ORM exists
- Explain Hibernate architecture
- Understand EntityManager and Persistence Context
- Explain the lifecycle of an entity
- Understand Hibernate Session
- Explain dirty checking
- Understand first-level caching
- Understand how Hibernate communicates with the database

---

# 🤔 Why Do We Need ORM?

Java applications work with **objects**.

Databases work with **tables**.

These two worlds are fundamentally different.

## Java Object

```java
Student student = new Student();

student.setId(1L);
student.setName("Rahul");
student.setDepartment("Computer Science");
```

---

## Database Table

| id | name | department |
|----|------|------------|
| 1 | Rahul | Computer Science |

Someone has to convert:

- Objects → Tables
- Tables → Objects

Without ORM, developers would manually write SQL for every operation.

---

# Problems Without ORM

Suppose we want to save a student.

Without ORM:

```text
Create SQL

↓

Open Connection

↓

Execute SQL

↓

Handle Exceptions

↓

Commit Transaction

↓

Close Connection
```

Now imagine performing this process hundreds of times.

Enterprise applications may contain thousands of SQL statements.

Maintaining them becomes expensive and error-prone.

ORM automates these repetitive tasks.

---

# What is ORM?

**ORM (Object Relational Mapping)** is a programming technique that maps Java objects to relational database tables.

Instead of thinking in terms of SQL tables, developers work with Java objects.

The ORM framework automatically generates SQL statements behind the scenes.

---

# Object to Table Mapping

```text
Java Object

Student

↓

ORM Framework

↓

students Table
```

---

# Table to Object Mapping

```text
students Table

↓

ORM Framework

↓

Student Object
```

---

# Real-Life Analogy

Imagine travelling to another country.

You speak English.

The local people speak Japanese.

Instead of learning an entirely new language, you use a translator.

```text
English

↓

Translator

↓

Japanese
```

Similarly,

```text
Java Objects

↓

Hibernate

↓

Database Tables
```

Hibernate acts as the translator.

---

# What is Hibernate?

Hibernate is an **Object Relational Mapping (ORM) framework** for Java.

It performs tasks such as:

- Mapping entities
- Generating SQL
- Managing transactions
- Caching objects
- Tracking changes
- Managing relationships

Hibernate implements the **Jakarta Persistence (JPA)** specification.

---

# Hibernate Architecture

```text
Spring Boot Application

↓

Spring Data JPA

↓

Hibernate

↓

JDBC

↓

Database
```

Each layer has a specific responsibility.

---

# Responsibilities of Hibernate

Hibernate performs numerous tasks automatically.

Examples include:

- Creating SQL
- Executing SQL
- Managing object states
- Managing transactions
- Loading related objects
- Caching frequently used entities
- Synchronising objects with the database

Without Hibernate, developers would perform all these tasks manually.

---

# The Mapping Process

Consider the following entity:

```java
@Entity
public class Student {

    @Id
    private Long id;

    private String name;

    private String email;

}
```

Hibernate maps this class to a database table.

```text
Student Class

↓

Hibernate

↓

students Table
```

Each field becomes a column.

---

# Hibernate in Action

When we execute:

```java
studentRepository.save(student);
```

Hibernate internally generates SQL similar to:

```sql
INSERT INTO students
(id, name, email)
VALUES
(1, 'Rahul', 'rahul@gmail.com');
```

Developers don't have to write this SQL themselves.

---

# Hibernate Session

A **Session** is Hibernate's primary interface for interacting with the database.

A Session is responsible for:

- Saving entities
- Updating entities
- Deleting entities
- Loading entities
- Tracking entity changes

In Spring Boot applications, Session management is handled automatically through Spring Data JPA.

---

# EntityManager

JPA does not directly expose Hibernate's Session.

Instead, it provides the **EntityManager** interface.

```text
Application

↓

EntityManager

↓

Hibernate Session

↓

Database
```

EntityManager provides standard operations such as:

- persist()
- merge()
- remove()
- find()

Hibernate executes these operations internally.

---

# Persistence Context

The Persistence Context is one of the most important concepts in JPA.

It acts as a container that manages entities currently being used by the application.

```text
Database

↓

Entity Loaded

↓

Persistence Context

↓

Java Object
```

Once an entity enters the Persistence Context, Hibernate keeps track of every change made to it.

---

# Entity Lifecycle

Every entity passes through several states.

```text
New

↓

Managed

↓

Detached

↓

Removed
```

Let's understand each state.

---

## 1. New (Transient)

The object exists only in memory.

```java
Student student = new Student();
```

It has not yet been saved.

---

## 2. Managed (Persistent)

The entity is now managed by Hibernate.

```java
entityManager.persist(student);
```

Hibernate tracks every modification.

---

## 3. Detached

The object still exists but is no longer managed.

Changes made to detached entities are not automatically saved.

---

## 4. Removed

The entity is marked for deletion.

```java
entityManager.remove(student);
```

During transaction commit, Hibernate deletes the record.

---

# Entity Lifecycle Diagram

```text
New

↓

persist()

↓

Managed

↓

detach()

↓

Detached

↓

remove()

↓

Removed
```

---

# Dirty Checking

Dirty Checking is one of Hibernate's most powerful features.

Suppose we load a student.

```java
Student student = repository.findById(1L).get();

student.setName("Amit");
```

Notice that we never call an update method.

During transaction commit,

Hibernate detects that the entity has changed.

```text
Managed Entity

↓

Hibernate Detects Changes

↓

UPDATE SQL Generated
```

This automatic detection is called **Dirty Checking**.

---

# First-Level Cache

Every Hibernate Session contains a cache.

```text
Application

↓

Session Cache

↓

Database
```

Suppose the application loads the same student twice.

Without caching:

```text
Database

↓

Database
```

Two database calls.

With caching:

```text
Database

↓

Cache
```

Only one database call.

This improves application performance.

---

# Benefits of First-Level Cache

- Faster response time
- Fewer SQL queries
- Reduced database load
- Better application performance

This cache is enabled automatically.

---

# Lazy Loading vs Eager Loading

Consider a Student and Course relationship.

Should Hibernate load courses immediately?

Or only when required?

This behaviour is controlled by Fetch Types.

```text
Student

↓

Courses
```

Two strategies exist.

## Lazy Loading

Load related data only when required.

```text
Student Loaded

↓

Courses Not Loaded
```

Later,

```text
Access Courses

↓

Courses Loaded
```

---

## Eager Loading

Everything loads immediately.

```text
Student Loaded

↓

Courses Loaded

↓

Department Loaded

↓

Teacher Loaded
```

Although convenient, excessive eager loading can significantly reduce performance.

---

# How Hibernate Executes SQL

The typical request flow is:

```text
REST API

↓

Controller

↓

Service

↓

Repository

↓

Hibernate

↓

JDBC

↓

Database
```

Hibernate generates SQL and JDBC executes it.

---

# Hibernate Advantages

Hibernate provides numerous benefits:

- Automatic SQL generation
- Object mapping
- Caching
- Dirty Checking
- Transaction management
- Relationship mapping
- Database independence
- Better maintainability
- Less boilerplate code

---

# Industry Insight

Nearly every enterprise Java backend uses an ORM framework.

Examples include:

- Banking systems
- Healthcare platforms
- Insurance software
- Government portals
- ERP systems
- HR applications
- E-commerce platforms

Understanding Hibernate is therefore essential for Java backend developers.

---

# Best Practices

- Think in terms of objects, not tables.
- Keep entities focused on persistence.
- Avoid unnecessary eager loading.
- Use transactions for data modifications.
- Let Hibernate manage entity states.
- Understand the Persistence Context before writing complex applications.

---

# Common Mistakes

- Confusing Hibernate with JPA.
- Believing ORM completely replaces SQL.
- Updating detached entities without merging.
- Loading too much data eagerly.
- Ignoring caching behaviour.

---

# Interview Corner

## Q1. What is ORM?

ORM (Object Relational Mapping) is a technique that maps Java objects to relational database tables.

---

## Q2. What is Hibernate?

Hibernate is an ORM framework that implements the JPA specification and automatically maps Java objects to database tables.

---

## Q3. What is the Persistence Context?

The Persistence Context is a container that manages entity objects and tracks their state during a transaction.

---

## Q4. What is Dirty Checking?

Dirty Checking is Hibernate's ability to automatically detect changes made to managed entities and generate the appropriate SQL UPDATE statements.

---

## Q5. What is the difference between Lazy and Eager Loading?

Lazy Loading fetches related data only when required, whereas Eager Loading retrieves related data immediately when the parent entity is loaded.

---

## Q6. What is the First-Level Cache?

The First-Level Cache is a Session-level cache that stores managed entities, reducing repeated database queries within the same session.

---

# 🧪 Hands-on Lab

## Objective

Observe how Hibernate automatically manages entity states.

### Tasks

1. Create a Spring Boot project with Spring Data JPA.
2. Add a Student entity.
3. Save a student using `save()`.
4. Retrieve the student.
5. Modify the student's name.
6. Commit the transaction.
7. Enable SQL logging.
8. Observe the generated SQL statements.
9. Load the same entity twice within a transaction and observe that only one SQL SELECT is executed due to the first-level cache.

---

# 📄 Cheat Sheet

| Concept | Description |
|----------|-------------|
| ORM | Maps objects to tables |
| Hibernate | ORM framework |
| JPA | Persistence specification |
| EntityManager | Standard JPA interface |
| Session | Hibernate persistence interface |
| Persistence Context | Tracks managed entities |
| Dirty Checking | Detects object changes automatically |
| First-Level Cache | Session-level cache |
| Lazy Loading | Loads related data on demand |
| Eager Loading | Loads related data immediately |

---

# 📝 Chapter Summary

In this chapter, you learned how Object Relational Mapping bridges the gap between object-oriented programming and relational databases. You explored Hibernate's architecture, the role of the EntityManager and Persistence Context, the lifecycle of an entity, dirty checking, first-level caching, and fetch strategies.

These concepts form the foundation of every Spring Data JPA application. Understanding how Hibernate works internally will help you write efficient, maintainable, and production-ready persistence code throughout the rest of this module.

---

# 🚀 What's Next?

In **Chapter 3 – Configuring Spring Data JPA**, you'll configure Spring Boot to work with H2, MySQL, and PostgreSQL, add the required dependencies, configure datasources, enable SQL logging, and prepare the Student Management System for persistent storage.
