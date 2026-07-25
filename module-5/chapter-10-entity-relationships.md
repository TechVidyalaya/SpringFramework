---
title: Entity Relationships
module: Module 5 – Spring Data JPA & Hibernate
chapter: 10
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Intermediate
estimated_reading_time: 60 Minutes
estimated_practical_time: 90 Minutes
---

# Chapter 10
# Entity Relationships

> **"Real-world applications are not built using isolated tables. Data is interconnected, and JPA allows us to model these relationships naturally using Java objects."**

---

# 📖 Introduction

So far, our `Student` entity has existed independently.

However, in real-world applications, entities are connected.

For example:

- A student belongs to one department.
- A department has many students.
- A student can enrol in multiple courses.
- A course can have many students.
- Every employee has one ID card.
- Every customer can place many orders.

Representing these relationships correctly is one of the most important skills in JPA and Hibernate.

This chapter introduces the four primary relationship types:

- One-to-One
- One-to-Many
- Many-to-One
- Many-to-Many

You'll also learn about cascading, fetch strategies, owning and inverse sides, and common pitfalls.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand entity relationships
- Implement One-to-One mapping
- Implement One-to-Many mapping
- Implement Many-to-One mapping
- Implement Many-to-Many mapping
- Understand foreign keys
- Configure cascading
- Choose appropriate fetch strategies
- Understand owning and inverse sides

---

# Why Do We Need Relationships?

Imagine the following tables.

```text
Departments

-------------------------
id
name
-------------------------

Students

-------------------------
id
name
department_id
-------------------------
```

Instead of storing the department name repeatedly, the student table stores a reference to the department.

This:

- Reduces duplication
- Improves consistency
- Saves storage
- Maintains referential integrity

---

# Relationship Types

```text
One-to-One

Person  ───────── Passport

One-to-Many

Department ───── Students

Many-to-One

Student ───── Department

Many-to-Many

Students ───── Courses
```

---

# One-to-One Relationship

One record is associated with exactly one other record.

Example:

```text
Employee

↓

ID Card
```

Database

```text
employees

-----------------
id
name
-----------------

employee_cards

-----------------
id
card_number
employee_id
-----------------
```

---

# One-to-One Mapping

Employee

```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToOne
    @JoinColumn(name = "card_id")
    private EmployeeCard card;

}
```

EmployeeCard

```java
@Entity
public class EmployeeCard {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String cardNumber;

}
```

---

# @JoinColumn

`@JoinColumn` specifies the foreign key column.

```java
@JoinColumn(name = "card_id")
```

Database

```text
employees

------------------------
id
name
card_id
------------------------
```

---

# One-to-Many Relationship

One department has many students.

```text
Department

↓

Student

Student

Student
```

Database

```text
departments

-------------
id
name
-------------

students

-------------
id
name
department_id
-------------
```

---

# Department Entity

```java
@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "department")
    private List<Student> students;

}
```

---

# Student Entity

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```

The `department_id` column acts as the foreign key.

---

# Understanding mappedBy

```java
@OneToMany(mappedBy = "department")
private List<Student> students;
```

The `mappedBy` attribute tells Hibernate that the relationship is managed by the `department` field in the `Student` entity.

Without `mappedBy`, Hibernate creates an unnecessary join table.

---

# Many-to-One Relationship

Many students belong to one department.

```text
Student A

↓

Student B

↓

Student C

↓

Department
```

This is the most common relationship in enterprise applications.

---

# Many-to-One Example

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```

Generated table

```text
students

-------------------------
id
name
department_id
-------------------------
```

---

# Many-to-Many Relationship

Students can enrol in multiple courses.

Courses can have multiple students.

```text
Students

⇄

Courses
```

A third table stores the relationship.

---

# Join Table

```text
students

courses

student_courses
```

Join Table

```text
student_id

course_id
```

---

# Many-to-Many Mapping

Student

```java
@ManyToMany
@JoinTable(
    name = "student_courses",
    joinColumns =
        @JoinColumn(name = "student_id"),
    inverseJoinColumns =
        @JoinColumn(name = "course_id")
)
private List<Course> courses;
```

Course

```java
@ManyToMany(mappedBy = "courses")
private List<Student> students;
```

---

# Relationship Summary

| Relationship | Annotation |
|--------------|------------|
| One-to-One | `@OneToOne` |
| One-to-Many | `@OneToMany` |
| Many-to-One | `@ManyToOne` |
| Many-to-Many | `@ManyToMany` |

---

# Cascade Operations

Cascade determines what happens to related entities.

```java
@OneToMany(
cascade = CascadeType.ALL
)
private List<Student> students;
```

Saving a department automatically saves its students.

---

# Cascade Types

| Cascade Type | Description |
|--------------|-------------|
| ALL | Apply all operations |
| PERSIST | Save child entities |
| MERGE | Update child entities |
| REMOVE | Delete child entities |
| REFRESH | Refresh from database |
| DETACH | Detach from persistence context |

---

# Fetch Types

Fetch type determines when related entities are loaded.

Two options exist.

```text
EAGER

LAZY
```

---

# EAGER Fetch

Data is loaded immediately.

```java
@OneToOne(fetch = FetchType.EAGER)
```

Example

Loading Student

↓

Department also loads immediately.

---

# LAZY Fetch

Related data is loaded only when required.

```java
@ManyToOne(fetch = FetchType.LAZY)
```

Loading Student

↓

Department loads only when accessed.

---

# EAGER vs LAZY

| EAGER | LAZY |
|--------|------|
| Immediate loading | On-demand loading |
| More memory usage | Better performance |
| Simple to use | Preferred for large applications |

**Recommendation:** Prefer `LAZY` for most relationships unless immediate loading is required.

---

# Owning Side vs Inverse Side

In a bidirectional relationship:

Owning Side

```java
@JoinColumn
```

Inverse Side

```java
mappedBy
```

The owning side updates the foreign key in the database.

---

# Bidirectional Relationship

```text
Department

↓

Students

↑
```

Navigation is possible from both entities.

---

# Unidirectional Relationship

```text
Student

↓

Department
```

Navigation is possible only from `Student`.

Unidirectional relationships are simpler and should be preferred unless navigation is required in both directions.

---

# Avoid Infinite Recursion

Consider the following.

```text
Department

↓

Students

↓

Department

↓

Students
```

When converting entities to JSON, this circular reference causes infinite recursion.

Solution:

```java
@JsonManagedReference
```

```java
@JsonBackReference
```

Or use DTOs, which is the recommended approach for REST APIs.

---

# Entity Relationship Diagram

```text
Department
------------------
id
name
------------------
       ▲
       │
       │ Many-to-One
       │
Student
------------------
id
name
email
department_id
------------------
       ▲
       │
       │ Many-to-Many
       │
Course
------------------
id
title
------------------
```

---

# Best Practices

- Prefer `LAZY` loading.
- Avoid `CascadeType.ALL` unless appropriate.
- Use DTOs in REST APIs.
- Keep relationships simple.
- Model relationships based on business requirements.
- Avoid unnecessary bidirectional mappings.

---

# Common Mistakes

❌ Forgetting `mappedBy`.

❌ Using `EAGER` everywhere.

❌ Creating circular JSON responses.

❌ Using `CascadeType.ALL` without understanding its impact.

❌ Creating unnecessary Many-to-Many relationships.

---

# Industry Insight

Relationships form the backbone of enterprise applications.

Examples include:

| Domain | Relationship |
|---------|--------------|
| Banking | Customer → Accounts |
| E-Commerce | Customer → Orders |
| Education | Student → Courses |
| Hospital | Doctor → Patients |
| HR | Employee → Department |
| CRM | Customer → Contacts |

Well-designed relationships improve maintainability, performance, and data integrity.

---

# 🧪 Hands-on Lab

## Objective

Model relationships for the Student Management System.

### Tasks

1. Create a `Department` entity.
2. Add a `ManyToOne` relationship from `Student` to `Department`.
3. Add a `OneToMany` relationship from `Department` to `Student`.
4. Create a `Course` entity.
5. Add a `ManyToMany` relationship between `Student` and `Course`.
6. Enable `LAZY` loading.
7. Test CRUD operations and verify the generated tables.

---

# 💼 Interview Corner

### Q1. What is the difference between `@OneToMany` and `@ManyToOne`?

`@OneToMany` represents one parent with multiple children, while `@ManyToOne` represents multiple child entities belonging to one parent.

---

### Q2. What is `mappedBy`?

`mappedBy` indicates the inverse side of a bidirectional relationship and prevents Hibernate from creating an extra join table.

---

### Q3. What is the purpose of `@JoinColumn`?

It specifies the foreign key column used to establish the relationship between two tables.

---

### Q4. What is the difference between `LAZY` and `EAGER` fetching?

- **LAZY** loads related entities only when needed.
- **EAGER** loads related entities immediately with the parent entity.

---

### Q5. What is the owning side of a relationship?

The owning side contains the `@JoinColumn` annotation and is responsible for updating the foreign key in the database.

---

# 📄 Cheat Sheet

| Annotation | Purpose |
|------------|---------|
| `@OneToOne` | One-to-One relationship |
| `@OneToMany` | One parent, many children |
| `@ManyToOne` | Many children, one parent |
| `@ManyToMany` | Many-to-Many relationship |
| `@JoinColumn` | Defines the foreign key |
| `@JoinTable` | Defines the join table |
| `mappedBy` | Specifies the inverse side |
| `CascadeType` | Controls cascade operations |
| `FetchType.LAZY` | Load on demand |
| `FetchType.EAGER` | Load immediately |

---

# 📝 Chapter Summary

In this chapter, you learned how to model real-world relationships using JPA and Hibernate. You explored One-to-One, One-to-Many, Many-to-One, and Many-to-Many mappings, understood the role of foreign keys, and learned how to use `@JoinColumn`, `@JoinTable`, and `mappedBy`.

You also discovered the importance of fetch strategies, cascade operations, and avoiding common issues such as infinite recursion in REST APIs.

---

# 🚀 What's Next?

In **Chapter 11 – Transaction Management**, you'll learn how Spring Boot ensures data consistency using transactions, explore ACID properties, understand the `@Transactional` annotation, handle rollbacks, and implement reliable business operations across multiple database actions.
