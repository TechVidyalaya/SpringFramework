
---
title: Entity Mapping
module: Module 5 – Spring Data JPA & Hibernate
chapter: 4
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Beginner
estimated_reading_time: 45 Minutes
estimated_practical_time: 60 Minutes
---

# Chapter 4
# Entity Mapping

> **"An entity is the bridge between your Java application and the database table."**

---

# 📖 Introduction

In the previous chapter, we configured Spring Data JPA and connected our Spring Boot application to a database.

Now it's time to create our first **Entity**.

An Entity is simply a Java class that represents a table in the database. Every object of that class represents one row of the table.

For example:

Database Table

| id | name | email |
|----|------|-------|
| 1 | Rahul | rahul@gmail.com |
| 2 | Priya | priya@gmail.com |

can be represented by the following Java class:

```java
Student student = new Student();
```

Hibernate automatically converts between Java objects and database rows.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Entity Mapping
- Create JPA entities
- Map Java classes to database tables
- Map object fields to table columns
- Configure primary keys
- Generate IDs automatically
- Understand commonly used JPA annotations
- Follow entity design best practices

---

# What is an Entity?

An **Entity** is a Java class that represents a database table.

Example:

```text
Student Class

↓

students Table
```

Every object represents one record.

```text
Student Object

↓

One Row
```

---

# Our Student Table

```text
students

----------------------------------------
id
name
email
department
date_of_birth
created_at
----------------------------------------
```

Equivalent Java object:

```java
Student student =
        new Student();
```

---

# Creating Our First Entity

```java
import jakarta.persistence.*;

@Entity
@Table(name = "students")
public class Student {

}
```

---

# @Entity

`@Entity` tells Hibernate that this class should be stored in the database.

```java
@Entity
public class Student {

}
```

Without this annotation, Hibernate ignores the class.

---

# @Table

By default, Hibernate creates a table using the class name.

If you want a custom table name:

```java
@Entity
@Table(name = "students")
public class Student {

}
```

Database table:

```text
students
```

---

# Creating Fields

```java
@Entity
@Table(name = "students")
public class Student {

    private Long id;

    private String name;

    private String email;

    private String department;

}
```

Each field becomes a database column.

---

# @Id

Every table requires a Primary Key.

```java
@Id
private Long id;
```

This uniquely identifies every record.

Example:

| id | name |
|----|------|
| 1 | Rahul |
| 2 | Amit |

No two records can have the same ID.

---

# @GeneratedValue

Instead of assigning IDs manually, let the database generate them.

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

Now when inserting:

```java
Student student = new Student();
```

Hibernate automatically generates:

```text
1

2

3

4

5
```

---

# Generation Strategies

| Strategy | Description |
|-----------|-------------|
| AUTO | Provider chooses strategy |
| IDENTITY | Database auto increment |
| SEQUENCE | Uses database sequence |
| TABLE | Uses a separate table for IDs |

Most Spring Boot applications use:

```java
GenerationType.IDENTITY
```

---

# @Column

Customizes column properties.

```java
@Column(name = "student_name")
private String name;
```

Database

```text
student_name
```

instead of

```text
name
```

---

# Common @Column Attributes

```java
@Column(
    nullable = false,
    unique = true,
    length = 100
)
private String email;
```

| Attribute | Purpose |
|-----------|----------|
| name | Column name |
| nullable | Allow NULL values |
| unique | Unique values |
| length | Maximum size |

---

# Complete Student Entity

```java
@Entity
@Table(name = "students")
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

    private String department;

    private LocalDate dateOfBirth;

}
```

---

# Mapping Process

```text
Student Object

↓

Hibernate

↓

students Table

↓

Database
```

---

# How Hibernate Creates Tables

If

```properties
spring.jpa.hibernate.ddl-auto=update
```

Hibernate automatically creates:

```sql
CREATE TABLE students (

id BIGINT PRIMARY KEY,

name VARCHAR(255),

email VARCHAR(255),

department VARCHAR(255),

date_of_birth DATE

);
```

No manual SQL required.

---

# @Transient

Sometimes a field should not be stored.

Example:

```java
@Transient
private int age;
```

Age can be calculated from the date of birth.

Hibernate ignores this field.

---

# @Enumerated

Suppose students have status.

```java
public enum Status {

    ACTIVE,
    INACTIVE

}
```

Entity

```java
@Enumerated(EnumType.STRING)
private Status status;
```

Database

```text
ACTIVE
```

instead of

```text
0
```

Using `EnumType.STRING` is recommended because it is more readable and safer when enum values change.

---

# @Lob

Large Object.

Useful for:

- Images
- PDFs
- Documents

Example:

```java
@Lob
private byte[] profilePhoto;
```

---

# @CreationTimestamp

Automatically stores creation time.

```java
@CreationTimestamp
private LocalDateTime createdAt;
```

No need to set manually.

---

# @UpdateTimestamp

Stores last update time.

```java
@UpdateTimestamp
private LocalDateTime updatedAt;
```

Automatically updated.

---

# Naming Strategy

Java

```java
dateOfBirth
```

Database

```text
date_of_birth
```

Hibernate automatically converts camelCase to snake_case by default.

---

# Entity Best Practices

✅ Keep entities simple.

✅ One entity = One table.

✅ Use wrapper classes (`Long`, `Integer`) instead of primitives where nullability matters.

✅ Avoid business logic inside entities.

✅ Use DTOs for API communication.

✅ Keep entity fields private.

---

# Common Mistakes

❌ Missing `@Entity`

❌ Missing `@Id`

❌ Exposing entities directly in REST APIs

❌ Using primitive types for nullable columns

❌ Storing calculated values unnecessarily

---

# Industry Insight

A single enterprise application may contain hundreds of entity classes.

Examples:

- Student
- Teacher
- Course
- Department
- Employee
- Product
- Customer
- Order
- Invoice

Good entity design makes the application easier to maintain and scale.

---

# 🧪 Hands-on Lab

## Objective

Create the `Student` entity.

### Tasks

1. Create a `Student` class.
2. Add `@Entity`.
3. Add `@Table`.
4. Create the following fields:
   - id
   - name
   - email
   - department
   - dateOfBirth
5. Configure `@Id` and `@GeneratedValue`.
6. Make `email` unique.
7. Run the application.
8. Verify that the `students` table is created automatically.

---

# 💼 Interview Corner

### Q1. What is an Entity?

An Entity is a Java class that maps to a database table.

---

### Q2. What does `@Entity` do?

It tells Hibernate that the class should be persisted as a database table.

---

### Q3. Why is `@Id` required?

Every entity must have a primary key to uniquely identify each record.

---

### Q4. What is `@GeneratedValue`?

It automatically generates primary key values.

---

### Q5. What is `@Transient`?

It marks a field that should not be persisted in the database.

---

### Q6. Why use `EnumType.STRING`?

Because it stores readable values and prevents issues if enum ordering changes.

---

# 📄 Cheat Sheet

| Annotation | Purpose |
|------------|---------|
| `@Entity` | Marks a class as a database entity |
| `@Table` | Specifies the table name |
| `@Id` | Primary key |
| `@GeneratedValue` | Auto-generates IDs |
| `@Column` | Customises column properties |
| `@Transient` | Excludes a field from persistence |
| `@Enumerated` | Maps enum values |
| `@Lob` | Stores large objects |
| `@CreationTimestamp` | Stores creation timestamp |
| `@UpdateTimestamp` | Stores last modification timestamp |

---

# 📝 Chapter Summary

In this chapter, you learned how Java classes are mapped to database tables using JPA annotations. You created your first `Student` entity and explored annotations such as `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column`, `@Transient`, `@Enumerated`, `@Lob`, `@CreationTimestamp`, and `@UpdateTimestamp`.

You also learned entity design best practices and how Hibernate automatically creates database tables from entity classes.

---

# 🚀 What's Next?

In **Chapter 5 – Repository Layer**, you'll learn how to create repository interfaces using `JpaRepository` and perform CRUD operations without writing SQL.
