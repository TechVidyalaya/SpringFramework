
---
title: JPQL (Java Persistence Query Language)
module: Module 5 – Spring Data JPA & Hibernate
chapter: 8
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Intermediate
estimated_reading_time: 50 Minutes
estimated_practical_time: 60 Minutes
---

# Chapter 8
# JPQL (Java Persistence Query Language)

> **"Derived Query Methods are perfect for simple queries. When business requirements become more complex, JPQL gives you complete control while still working with Java objects instead of database tables."**

---

# 📖 Introduction

In the previous chapter, we learned how Spring Data JPA automatically generates SQL queries using method names.

Although Derived Query Methods are powerful, they have limitations.

Consider the following requirements:

- Find students whose department is "Computer Science" and whose name starts with "A".
- Retrieve only student names and emails.
- Update multiple records at once.
- Perform aggregate operations like counting students in each department.

Creating long method names for these requirements quickly becomes difficult.

This is where **JPQL (Java Persistence Query Language)** becomes useful.

JPQL allows developers to write custom queries while still working with **Java entities and fields**, rather than database tables and columns.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand JPQL
- Write custom queries
- Use the `@Query` annotation
- Work with named parameters
- Select specific fields
- Write update and delete queries
- Use aggregate functions
- Return DTOs using JPQL

---

# What is JPQL?

**JPQL (Java Persistence Query Language)** is a query language defined by JPA.

Unlike SQL, JPQL works with:

- Entity classes
- Entity fields

instead of

- Database tables
- Database columns

---

# SQL vs JPQL

Database Table

```text
students
```

Entity

```java
Student
```

SQL

```sql
SELECT *
FROM students;
```

JPQL

```java
SELECT s
FROM Student s
```

Notice the difference.

JPQL uses:

- `Student` (Entity)
- `name` (Java field)

instead of

- `students`
- `student_name`

---

# Using @Query

JPQL queries are written using the `@Query` annotation.

```java
@Query("SELECT s FROM Student s")
List<Student> findAllStudents();
```

Spring Data JPA automatically executes the query.

---

# Selecting All Students

```java
@Query("SELECT s FROM Student s")
List<Student> getStudents();
```

Equivalent SQL

```sql
SELECT *
FROM students;
```

---

# Find by Department

```java
@Query("""
SELECT s
FROM Student s
WHERE s.department = :department
""")
List<Student> findStudentsByDepartment(
        String department);
```

---

# Named Parameters

Named parameters make queries easier to read.

```java
@Query("""
SELECT s
FROM Student s
WHERE s.email = :email
""")
Optional<Student> findByEmail(
        @Param("email") String email);
```

---

# Multiple Conditions

```java
@Query("""
SELECT s
FROM Student s
WHERE s.department = :department
AND s.name = :name
""")
List<Student> search(
        @Param("department") String department,
        @Param("name") String name);
```

---

# Sorting Results

```java
@Query("""
SELECT s
FROM Student s
ORDER BY s.name ASC
""")
List<Student> findStudents();
```

Descending

```java
ORDER BY s.name DESC
```

---

# Selecting Specific Fields

Sometimes we don't need every column.

```java
@Query("""
SELECT s.name
FROM Student s
""")
List<String> findNames();
```

Only names are returned.

---

# Aggregate Functions

JPQL supports aggregate functions.

Count

```java
@Query("""
SELECT COUNT(s)
FROM Student s
""")
long totalStudents();
```

Average

```java
@Query("""
SELECT AVG(s.id)
FROM Student s
""")
Double averageId();
```

Maximum

```java
SELECT MAX(s.id)
```

Minimum

```java
SELECT MIN(s.id)
```

---

# LIKE Query

```java
@Query("""
SELECT s
FROM Student s
WHERE s.name LIKE %:keyword%
""")
List<Student> search(String keyword);
```

Searching for

```
Rah
```

returns

```
Rahul

Rahul Sharma
```

---

# IN Query

```java
@Query("""
SELECT s
FROM Student s
WHERE s.department IN :departments
""")
List<Student> findDepartments(
        List<String> departments);
```

---

# Update Query

JPQL also supports update operations.

```java
@Modifying
@Transactional
@Query("""
UPDATE Student s
SET s.department = :department
WHERE s.id = :id
""")
void updateDepartment(
        Long id,
        String department);
```

`@Modifying` tells Spring Data JPA that this is not a SELECT query.

---

# Delete Query

```java
@Modifying
@Transactional
@Query("""
DELETE
FROM Student s
WHERE s.id = :id
""")
void deleteStudent(Long id);
```

---

# Returning DTOs

Suppose we only need name and email.

DTO

```java
public class StudentResponse {

    private String name;

    private String email;

}
```

JPQL

```java
@Query("""
SELECT new
com.techvidyalaya.dto.StudentResponse(
s.name,
s.email)
FROM Student s
""")
List<StudentResponse> getStudents();
```

Only required fields are retrieved.

---

# Repository Example

```java
public interface StudentRepository
        extends JpaRepository<Student, Long> {

    @Query("SELECT s FROM Student s")
    List<Student> findAllStudents();

    @Query("""
        SELECT s
        FROM Student s
        WHERE s.department = :department
    """)
    List<Student> findByDepartment(
            @Param("department") String department);

}
```

---

# Query Execution Flow

```text
Controller

↓

Service

↓

Repository

↓

JPQL

↓

Hibernate

↓

SQL

↓

Database
```

Hibernate converts JPQL into SQL before executing it.

---

# JPQL Advantages

- Database independent
- Works with entities
- Easier to maintain
- Supports joins
- Supports aggregation
- Supports DTO projection
- Cleaner than native SQL

---

# JPQL Limitations

JPQL cannot:

- Use every database-specific feature
- Access vendor-specific SQL functions easily
- Replace complex reporting SQL completely

For these scenarios, use **Native SQL Queries**.

---

# Best Practices

- Use JPQL for complex business queries.
- Use named parameters instead of positional parameters.
- Prefer DTO projections when only a few fields are needed.
- Keep queries readable.
- Avoid very long JPQL statements.

---

# Common Mistakes

❌ Using table names instead of entity names.

❌ Using column names instead of entity fields.

❌ Forgetting `@Modifying` for update/delete queries.

❌ Returning entire entities when only a few fields are required.

---

# Industry Insight

JPQL is widely used for:

- Search screens
- Dashboard reports
- Filtering
- Business reports
- Data aggregation
- Custom APIs

Most enterprise applications use a combination of:

- Derived Query Methods
- JPQL
- Native SQL

depending on the complexity of the requirement.

---

# 🧪 Hands-on Lab

## Objective

Create custom JPQL queries for the Student Management System.

### Tasks

1. Retrieve all students using JPQL.
2. Find students by department.
3. Search students by email.
4. Return only student names.
5. Count total students.
6. Update a student's department using JPQL.
7. Delete a student using JPQL.

---

# 💼 Interview Corner

### Q1. What is JPQL?

JPQL (Java Persistence Query Language) is a query language that works with JPA entities and their fields rather than database tables and columns.

---

### Q2. What is the difference between SQL and JPQL?

SQL works with database tables and columns, while JPQL works with entity classes and entity properties.

---

### Q3. Why is `@Query` used?

It allows developers to define custom JPQL or SQL queries inside repository interfaces.

---

### Q4. When is `@Modifying` required?

`@Modifying` is required for JPQL update and delete queries because they modify database records.

---

### Q5. Why use DTO projection?

DTO projections improve performance by retrieving only the required fields instead of the entire entity.

---

# 📄 Cheat Sheet

| Feature | Example |
|---------|---------|
| Select | `SELECT s FROM Student s` |
| Filter | `WHERE s.department = :department` |
| Sort | `ORDER BY s.name ASC` |
| Count | `COUNT(s)` |
| Like | `LIKE %:keyword%` |
| Update | `UPDATE Student s ...` |
| Delete | `DELETE FROM Student s ...` |
| DTO Projection | `SELECT new StudentResponse(...)` |

---

# 📝 Chapter Summary

In this chapter, you learned how JPQL provides greater flexibility than Derived Query Methods while still allowing you to work with Java entities instead of database tables. You created custom queries using `@Query`, used named parameters, performed updates and deletes, executed aggregate functions, and returned DTO projections.

JPQL is ideal for medium-complexity business queries where method names become difficult to manage but full native SQL is not yet required.

---

# 🚀 What's Next?

In **Chapter 9 – Native SQL Queries**, you'll learn how to execute database-specific SQL statements, use advanced SQL features, call stored procedures, and optimize performance for complex business requirements.
