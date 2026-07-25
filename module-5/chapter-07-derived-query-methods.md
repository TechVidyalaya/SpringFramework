---
title: Derived Query Methods
module: Module 5 – Spring Data JPA & Hibernate
chapter: 7
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_practical_time: 60 Minutes
---

# Chapter 7
# Derived Query Methods

> **"One of the biggest strengths of Spring Data JPA is its ability to generate SQL queries simply by reading your method names."**

---

# 📖 Introduction

In the previous chapter, we learned how to perform basic CRUD operations using `JpaRepository`.

But enterprise applications rarely stop at simple CRUD.

Users often need to search for data.

Examples include:

- Find a student by email.
- Find all students in a department.
- Find students whose names start with "A".
- Find students born after a specific date.
- Check whether an email already exists.

Instead of writing SQL or JPQL for these common queries, Spring Data JPA allows us to create **Derived Query Methods**.

Spring automatically reads the method name, understands what we want, and generates the required SQL behind the scenes.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Derived Query Methods
- Create custom finder methods
- Generate SQL automatically
- Use query keywords
- Combine multiple conditions
- Sort query results
- Write readable repository methods
- Follow naming conventions

---

# What are Derived Query Methods?

Derived Query Methods are repository methods whose SQL is automatically generated from their names.

Example:

```java
findByEmail(String email)
```

Spring Data JPA automatically generates:

```sql
SELECT *
FROM students
WHERE email = ?;
```

No SQL.

No JPQL.

No implementation class.

---

# Creating Query Methods

Inside `StudentRepository`

```java
public interface StudentRepository
        extends JpaRepository<Student, Long> {

}
```

Add your own methods.

---

# Find by Name

```java
List<Student> findByName(String name);
```

Generated SQL

```sql
SELECT *
FROM students
WHERE name = ?;
```

---

# Find by Email

```java
Optional<Student> findByEmail(String email);
```

Generated SQL

```sql
SELECT *
FROM students
WHERE email = ?;
```

Using `Optional` is recommended because email is typically unique.

---

# Find by Department

```java
List<Student> findByDepartment(String department);
```

Generated SQL

```sql
SELECT *
FROM students
WHERE department = ?;
```

---

# Multiple Conditions

Find students by both department and name.

```java
List<Student> findByDepartmentAndName(
        String department,
        String name);
```

Generated SQL

```sql
SELECT *
FROM students
WHERE department = ?
AND name = ?;
```

---

# Using OR

```java
List<Student> findByDepartmentOrName(
        String department,
        String name);
```

Generated SQL

```sql
SELECT *
FROM students
WHERE department = ?
OR name = ?;
```

---

# Ignore Case

```java
List<Student> findByNameIgnoreCase(String name);
```

Example

```
Rahul

rahul

RAHUL
```

All produce the same result.

---

# Contains

```java
List<Student> findByNameContaining(String keyword);
```

Example

```
Rahul Sharma

Anurag

Rahul Kumar
```

Searching for

```
Rah
```

returns all matching records.

Equivalent SQL

```sql
LIKE '%Rah%'
```

---

# Starts With

```java
List<Student> findByNameStartingWith(String prefix);
```

Searching for

```
A
```

returns

```
Amit

Anjali

Akash
```

---

# Ends With

```java
List<Student> findByNameEndingWith(String suffix);
```

Searching for

```
sh
```

returns

```
Harsh

Yogesh
```

---

# Like

```java
List<Student> findByNameLike(String pattern);
```

Example

```
Rah%
```

---

# Between

```java
List<Student> findByDateOfBirthBetween(
        LocalDate start,
        LocalDate end);
```

Useful for:

- Birth date
- Joining date
- Order date

---

# Greater Than

```java
List<Student> findByIdGreaterThan(Long id);
```

Generated SQL

```sql
WHERE id > ?
```

---

# Less Than

```java
List<Student> findByIdLessThan(Long id);
```

---

# Order By

```java
List<Student> findByDepartmentOrderByNameAsc(
        String department);
```

Generated SQL

```sql
ORDER BY name ASC
```

Descending

```java
findByDepartmentOrderByNameDesc(...)
```

---

# Exists Query

```java
boolean existsByEmail(String email);
```

Useful before registration.

Example

```java
if(repository.existsByEmail(email)){

    throw new RuntimeException("Email already exists");

}
```

---

# Count Query

```java
long countByDepartment(String department);
```

Generated SQL

```sql
SELECT COUNT(*)
```

---

# Top Records

Retrieve the first student.

```java
Student findFirstByOrderByIdAsc();
```

Retrieve top five.

```java
List<Student> findTop5ByOrderByIdDesc();
```

---

# Combining Keywords

```java
List<Student>
findByDepartmentAndNameStartingWithIgnoreCase(
        String department,
        String prefix);
```

Spring Data JPA understands even complex method names.

---

# Common Query Keywords

| Keyword | Example |
|----------|---------|
| By | findByName |
| And | findByNameAndDepartment |
| Or | findByNameOrDepartment |
| Between | findByAgeBetween |
| LessThan | findByIdLessThan |
| GreaterThan | findBySalaryGreaterThan |
| Like | findByNameLike |
| Containing | findByNameContaining |
| StartingWith | findByNameStartingWith |
| EndingWith | findByNameEndingWith |
| IgnoreCase | findByEmailIgnoreCase |
| OrderBy | findByDepartmentOrderByNameAsc |
| ExistsBy | existsByEmail |
| CountBy | countByDepartment |
| Top | findTop5ByOrderByIdDesc |
| First | findFirstByOrderByIdAsc |

---

# Repository Example

```java
public interface StudentRepository
        extends JpaRepository<Student, Long> {

    Optional<Student> findByEmail(String email);

    List<Student> findByDepartment(String department);

    List<Student> findByNameContaining(String keyword);

    List<Student> findByDepartmentAndName(
            String department,
            String name);

    boolean existsByEmail(String email);

    long countByDepartment(String department);

}
```

---

# Query Execution Flow

```text
Controller

↓

Service

↓

StudentRepository

↓

Spring Data JPA

↓

Generated SQL

↓

Database
```

No SQL is written by the developer.

---

# Advantages

- No SQL for simple queries
- Less boilerplate code
- Easy to read
- Easy to maintain
- Type-safe
- Faster development

---

# When Not to Use Derived Queries

Derived Query Methods are excellent for simple searches.

However, avoid them when:

- The method name becomes too long.
- Multiple joins are required.
- Complex filtering is needed.
- Aggregation queries are involved.

In such cases, use:

- JPQL
- Native SQL
- Specifications

These topics will be covered in upcoming chapters.

---

# Best Practices

- Keep method names readable.
- Use `Optional` for unique results.
- Use meaningful property names.
- Prefer derived queries for simple searches.
- Switch to JPQL for complex queries.

---

# Common Mistakes

❌ Misspelling entity field names.

❌ Creating extremely long method names.

❌ Using derived queries for highly complex searches.

❌ Returning a single object when multiple records may exist.

---

# Industry Insight

Derived Query Methods are heavily used in enterprise applications because they eliminate repetitive SQL.

Examples include:

- Login by email
- Search employees by department
- Find products by category
- Check username availability
- Retrieve active users
- Count orders by customer

They significantly improve developer productivity while keeping repository code clean.

---

# 🧪 Hands-on Lab

## Objective

Create search methods for the Student Management System.

### Tasks

1. Add `findByEmail()`.
2. Add `findByDepartment()`.
3. Add `findByNameContaining()`.
4. Add `findByDepartmentAndName()`.
5. Add `existsByEmail()`.
6. Add `countByDepartment()`.
7. Test all methods using sample data.

---

# 💼 Interview Corner

### Q1. What is a Derived Query Method?

A repository method whose SQL query is automatically generated from its name.

---

### Q2. Do we need to implement derived query methods?

No. Spring Data JPA generates the implementation automatically.

---

### Q3. Which method checks whether an email exists?

```java
existsByEmail(String email)
```

---

### Q4. Which method performs a LIKE search?

```java
findByNameContaining(String keyword)
```

---

### Q5. When should you avoid derived query methods?

When queries become too complex or method names become excessively long. In such cases, use JPQL or native SQL.

---

# 📄 Cheat Sheet

| Method | Purpose |
|---------|---------|
| `findByName()` | Search by name |
| `findByEmail()` | Search by email |
| `findByDepartment()` | Search by department |
| `findByNameContaining()` | LIKE `%value%` |
| `findByNameStartingWith()` | Prefix search |
| `findByNameEndingWith()` | Suffix search |
| `findByDepartmentAndName()` | Multiple conditions |
| `existsByEmail()` | Check existence |
| `countByDepartment()` | Count matching records |
| `findTop5ByOrderByIdDesc()` | Retrieve top records |

---

# 📝 Chapter Summary

In this chapter, you learned how Spring Data JPA can generate SQL queries automatically using **Derived Query Methods**. By following simple naming conventions, you can perform searches, filtering, counting, existence checks, and sorting without writing SQL or JPQL.

Derived Query Methods are ideal for common business queries and help keep repository code concise, readable, and easy to maintain.

---

# 🚀 What's Next?

In **Chapter 8 – JPQL (Java Persistence Query Language)**, you'll learn how to write custom queries when Derived Query Methods are no longer sufficient, giving you greater flexibility for complex business requirements.
