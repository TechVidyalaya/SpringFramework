---
title: CRUD Operations with JPA
module: Module 5 – Spring Data JPA & Hibernate
chapter: 6
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Beginner
estimated_reading_time: 45 Minutes
estimated_practical_time: 60 Minutes
---

# Chapter 6
# CRUD Operations with JPA

> **"Most enterprise applications revolve around one simple concept—creating, reading, updating, and deleting data."**

---

# 📖 Introduction

In the previous chapter, we created the `StudentRepository` by extending `JpaRepository`.

However, simply creating a repository is not enough.

Now we need to use it to build a complete CRUD (Create, Read, Update, Delete) application.

Spring Data JPA provides most CRUD operations out of the box. Instead of writing SQL statements, we simply invoke repository methods, allowing us to focus on business logic rather than database interaction.

By the end of this chapter, the Student Management System will be able to create, retrieve, update, and delete student records from the database.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand CRUD operations
- Save new entities
- Retrieve data
- Update existing records
- Delete records
- Count records
- Check record existence
- Build a complete Service Layer using `JpaRepository`

---

# What is CRUD?

CRUD represents the four fundamental database operations.

| Operation | Description |
|-----------|-------------|
| Create | Insert new data |
| Read | Retrieve existing data |
| Update | Modify existing data |
| Delete | Remove existing data |

Nearly every enterprise application performs these operations.

---

# CRUD Flow

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

---

# Student Service

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

}
```

---

# Create Operation

Creating a student means inserting a new record into the database.

```java
public Student saveStudent(Student student) {

    return repository.save(student);

}
```

Usage

```java
Student student = new Student();

student.setName("Rahul");
student.setEmail("rahul@gmail.com");

studentService.saveStudent(student);
```

Generated SQL

```sql
INSERT INTO students (...)
VALUES (...);
```

---

# Read Operation

Retrieve all students.

```java
public List<Student> getAllStudents() {

    return repository.findAll();

}
```

Generated SQL

```sql
SELECT *
FROM students;
```

---

# Retrieve Student by ID

```java
public Student getStudent(Long id) {

    return repository.findById(id)
            .orElseThrow(() ->
                new RuntimeException("Student not found"));

}
```

Generated SQL

```sql
SELECT *
FROM students
WHERE id = ?;
```

---

# Why Optional?

`findById()` returns an `Optional<Student>`.

This helps avoid `NullPointerException`.

```java
Optional<Student> student =
        repository.findById(1L);
```

If the student does not exist, the Optional is empty.

---

# Update Operation

Updating is surprisingly simple.

```java
public Student updateStudent(Long id, Student updatedStudent) {

    Student student = repository.findById(id)
            .orElseThrow(() ->
                new RuntimeException("Student not found"));

    student.setName(updatedStudent.getName());
    student.setEmail(updatedStudent.getEmail());
    student.setDepartment(updatedStudent.getDepartment());

    return repository.save(student);

}
```

Hibernate automatically generates an UPDATE statement.

---

# Update Flow

```text
Load Student

↓

Modify Object

↓

save()

↓

UPDATE SQL
```

---

# Delete Operation

Delete using ID.

```java
public void deleteStudent(Long id) {

    repository.deleteById(id);

}
```

Generated SQL

```sql
DELETE
FROM students
WHERE id = ?;
```

---

# Delete Using Entity

```java
Student student =
        repository.findById(id).get();

repository.delete(student);
```

---

# Count Records

Count all students.

```java
long totalStudents =
        repository.count();
```

Generated SQL

```sql
SELECT COUNT(*)
FROM students;
```

Useful for:

- Dashboards
- Reports
- Analytics

---

# Check Record Exists

```java
boolean exists =
        repository.existsById(id);
```

Generated SQL

```sql
SELECT COUNT(*)
FROM students
WHERE id = ?;
```

Useful before:

- Updating
- Deleting
- Creating relationships

---

# Complete Student Service

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

    public Student saveStudent(Student student) {
        return repository.save(student);
    }

    public List<Student> getAllStudents() {
        return repository.findAll();
    }

    public Student getStudent(Long id) {
        return repository.findById(id)
                .orElseThrow(() ->
                    new RuntimeException("Student not found"));
    }

    public Student updateStudent(Long id,
                                 Student updatedStudent) {

        Student student = getStudent(id);

        student.setName(updatedStudent.getName());
        student.setEmail(updatedStudent.getEmail());
        student.setDepartment(updatedStudent.getDepartment());

        return repository.save(student);
    }

    public void deleteStudent(Long id) {
        repository.deleteById(id);
    }

}
```

---

# save() – Insert or Update?

One interesting feature of `save()` is that it performs both insert and update operations.

### Insert

```java
Student student = new Student();

repository.save(student);
```

No ID exists.

Hibernate performs:

```sql
INSERT
```

---

### Update

```java
Student student =
        repository.findById(1L).get();

student.setName("Amit");

repository.save(student);
```

ID already exists.

Hibernate performs:

```sql
UPDATE
```

---

# CRUD Lifecycle

```text
Create

↓

Read

↓

Update

↓

Delete
```

These four operations form the backbone of most business applications.

---

# Student Management API

Our application now supports:

```text
POST   /students

GET    /students

GET    /students/{id}

PUT    /students/{id}

DELETE /students/{id}
```

---

# Best Practices

- Validate IDs before updating.
- Return meaningful exceptions.
- Keep CRUD logic inside the Service Layer.
- Keep Controllers lightweight.
- Avoid placing business logic inside repositories.
- Use DTOs instead of exposing entities through APIs.

---

# Common Mistakes

❌ Calling `get()` directly on an empty Optional.

❌ Updating without checking whether the entity exists.

❌ Writing SQL for basic CRUD operations.

❌ Placing CRUD logic inside Controllers.

❌ Returning entities directly in REST APIs.

---

# Industry Insight

Although CRUD operations appear simple, almost every enterprise application relies heavily on them.

Examples include:

- Banking systems
- Hospital management
- Inventory systems
- CRM software
- Student portals
- Airline booking systems

Spring Data JPA dramatically reduces the amount of code required to implement these operations.

---

# 🧪 Hands-on Lab

## Objective

Implement complete CRUD functionality.

### Tasks

1. Create `StudentService`.
2. Inject `StudentRepository`.
3. Implement:
   - `saveStudent()`
   - `getAllStudents()`
   - `getStudent()`
   - `updateStudent()`
   - `deleteStudent()`
4. Test each method using Postman.
5. Verify records in the database.

---

# 💼 Interview Corner

### Q1. What does `save()` do?

It inserts a new entity or updates an existing one based on whether the primary key already exists.

---

### Q2. Why does `findById()` return Optional?

To safely handle missing records and avoid `NullPointerException`.

---

### Q3. What is the difference between `delete()` and `deleteById()`?

- `delete()` removes an entity object.
- `deleteById()` removes a record using its primary key.

---

### Q4. Which repository method returns all records?

```java
findAll()
```

---

### Q5. Which repository method checks whether a record exists?

```java
existsById()
```

---

# 📄 Cheat Sheet

| Method | Description |
|---------|-------------|
| `save()` | Insert or update an entity |
| `findAll()` | Retrieve all records |
| `findById()` | Retrieve by primary key |
| `delete()` | Delete an entity |
| `deleteById()` | Delete using primary key |
| `count()` | Count total records |
| `existsById()` | Check if a record exists |

---

# 📝 Chapter Summary

In this chapter, you implemented complete CRUD functionality using Spring Data JPA. You learned how to create, retrieve, update, and delete student records without writing SQL. You also explored methods such as `save()`, `findAll()`, `findById()`, `deleteById()`, `count()`, and `existsById()`.

With these operations in place, your Student Management System can now perform all essential database interactions required by most enterprise applications.

---

# 🚀 What's Next?

In **Chapter 7 – Derived Query Methods**, you'll learn how Spring Data JPA can automatically generate database queries from method names, allowing you to build powerful search features without writing SQL.
