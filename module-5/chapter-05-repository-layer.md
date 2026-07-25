---
title: Repository Layer
module: Module 5 – Spring Data JPA & Hibernate
chapter: 5
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Beginner
estimated_reading_time: 40 Minutes
estimated_practical_time: 50 Minutes
---

# Chapter 5
# Repository Layer

> **"The Repository Layer acts as a bridge between your business logic and the database, allowing developers to perform database operations without writing SQL for every CRUD operation."**

---

# 📖 Introduction

In the previous chapter, we created our first JPA Entity called `Student`.

Now comes the next important question.

**How do we save a Student into the database?**

Should we write SQL manually?

```sql
INSERT INTO students ...
```

Should we use JDBC?

Fortunately, the answer is **No**.

Spring Data JPA provides **Repositories**, which automatically implement common database operations such as saving, updating, deleting, and retrieving records.

Instead of writing SQL, we simply call Java methods.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand the Repository Pattern
- Explain the Repository Layer
- Create a JPA Repository
- Use `JpaRepository`
- Understand the Repository hierarchy
- Perform basic CRUD operations
- Inject repositories into services
- Follow repository best practices

---

# What is a Repository?

A Repository is a Java interface responsible for communicating with the database.

It provides methods to:

- Save data
- Update data
- Delete data
- Retrieve data

Instead of writing SQL, developers call repository methods.

---

# Repository in Application Architecture

```text
Client

↓

Controller

↓

Service

↓

Repository

↓

Hibernate

↓

Database
```

The Service Layer never communicates directly with the database.

It always uses the Repository Layer.

---

# Repository Pattern

The Repository Pattern separates database operations from business logic.

Without Repository

```text
Service

↓

SQL

↓

Database
```

With Repository

```text
Service

↓

Repository

↓

Database
```

This keeps the code clean and maintainable.

---

# Repository Hierarchy

Spring Data provides several repository interfaces.

```text
Repository
      │
      ▼
CrudRepository
      │
      ▼
PagingAndSortingRepository
      │
      ▼
JpaRepository
```

Each level adds more functionality.

---

# Repository Interface

The root interface is:

```java
Repository<T, ID>
```

It is only a marker interface and does not provide CRUD methods.

---

# CrudRepository

Provides basic CRUD operations.

Some common methods:

- save()
- findById()
- findAll()
- delete()
- deleteById()
- existsById()
- count()

---

# PagingAndSortingRepository

Extends `CrudRepository`.

Additional features:

- Pagination
- Sorting

Useful when working with large datasets.

---

# JpaRepository

`JpaRepository` extends `PagingAndSortingRepository` and adds JPA-specific features.

It is the most commonly used repository interface in Spring Boot applications.

---

# Creating StudentRepository

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository
        extends JpaRepository<Student, Long> {

}
```

That's it!

Spring Boot automatically creates the implementation at runtime.

No implementation class is required.

---

# Generic Parameters

```java
JpaRepository<Student, Long>
```

The first parameter:

```text
Student
```

Entity class.

The second parameter:

```text
Long
```

Primary key type.

---

# Automatic CRUD Methods

Once the repository is created, the following methods become available automatically.

```java
save()
```

```java
findAll()
```

```java
findById()
```

```java
delete()
```

```java
deleteById()
```

```java
count()
```

No implementation is needed.

---

# Injecting Repository into Service

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

}
```

Constructor Injection is the recommended approach.

---

# Saving Data

```java
Student student = new Student();

student.setName("Rahul");

repository.save(student);
```

Hibernate automatically generates the required SQL.

---

# Retrieving Data

Retrieve all students.

```java
List<Student> students =
        repository.findAll();
```

Retrieve one student.

```java
Optional<Student> student =
        repository.findById(1L);
```

---

# Updating Data

Updating works the same way as saving.

```java
Student student =
        repository.findById(1L).get();

student.setDepartment("Computer Science");

repository.save(student);
```

Hibernate automatically performs an UPDATE operation.

---

# Deleting Data

Delete using an entity.

```java
repository.delete(student);
```

Delete using ID.

```java
repository.deleteById(1L);
```

---

# Counting Records

```java
long total =
        repository.count();
```

Useful for dashboards and reports.

---

# Checking Data Exists

```java
boolean exists =
        repository.existsById(1L);
```

Useful before updating or deleting a record.

---

# Repository Workflow

```text
Controller

↓

Service

↓

StudentRepository

↓

Hibernate

↓

Database
```

The repository hides all database complexity from the Service Layer.

---

# Why Use JpaRepository?

Advantages:

- Less boilerplate code
- Automatic CRUD implementation
- Pagination support
- Sorting support
- Query method generation
- Easy integration with Spring Boot
- Easy testing

---

# Student Management Example

When a client sends:

```http
POST /students
```

The flow is:

```text
REST Request

↓

Controller

↓

Service

↓

repository.save()

↓

Hibernate

↓

Database
```

---

# Best Practices

- Create one repository per entity.
- Keep repositories focused on database operations.
- Place business logic inside the Service Layer.
- Prefer constructor injection.
- Return `Optional` when retrieving single records.

---

# Common Mistakes

❌ Writing business logic inside repositories.

❌ Injecting repositories directly into controllers.

❌ Creating unnecessary custom SQL for simple CRUD operations.

❌ Using field injection instead of constructor injection.

---

# Industry Insight

Large enterprise applications often contain dozens or even hundreds of repositories.

Examples:

- StudentRepository
- CourseRepository
- TeacherRepository
- EmployeeRepository
- ProductRepository
- OrderRepository
- CustomerRepository

Each repository manages a single entity and follows the Single Responsibility Principle.

---

# 🧪 Hands-on Lab

## Objective

Create a repository for the `Student` entity.

### Tasks

1. Create a package named `repository`.
2. Create `StudentRepository`.
3. Extend `JpaRepository<Student, Long>`.
4. Inject the repository into `StudentService`.
5. Save a student.
6. Retrieve all students.
7. Retrieve a student by ID.
8. Delete a student.
9. Display the total number of students.

---

# 💼 Interview Corner

### Q1. What is a Repository?

A Repository is a component that provides an abstraction for performing database operations on entities.

---

### Q2. What is `JpaRepository`?

`JpaRepository` is a Spring Data interface that provides CRUD operations, pagination, sorting, and other JPA-specific functionality.

---

### Q3. Do we need to implement `StudentRepository`?

No.

Spring Boot automatically generates the implementation at runtime.

---

### Q4. Why should repositories not contain business logic?

Repositories should only handle data access. Business rules belong in the Service Layer to keep the application maintainable.

---

### Q5. Why is constructor injection preferred?

Constructor injection makes dependencies explicit, supports immutability, and simplifies unit testing.

---

# 📄 Cheat Sheet

| Interface | Purpose |
|------------|---------|
| `Repository` | Marker interface |
| `CrudRepository` | Basic CRUD operations |
| `PagingAndSortingRepository` | Pagination and sorting |
| `JpaRepository` | Full JPA functionality |

### Common Methods

| Method | Purpose |
|---------|---------|
| `save()` | Insert or update an entity |
| `findById()` | Retrieve by primary key |
| `findAll()` | Retrieve all records |
| `delete()` | Delete an entity |
| `deleteById()` | Delete by primary key |
| `existsById()` | Check if a record exists |
| `count()` | Count total records |

---

# 📝 Chapter Summary

In this chapter, you learned how the Repository Layer simplifies database access using Spring Data JPA. You explored the Repository hierarchy, created your first `StudentRepository`, and used built-in methods to perform CRUD operations without writing SQL.

By separating database access from business logic, repositories make applications easier to maintain, test, and extend.

---

# 🚀 What's Next?

In **Chapter 6 – CRUD Operations with JPA**, you'll build complete Create, Read, Update, and Delete APIs using the `StudentRepository`, connect them to the Service and Controller layers, and understand how Spring Data JPA generates SQL behind the scenes.
