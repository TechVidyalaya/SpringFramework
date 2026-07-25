
---
title: Native SQL Queries
module: Module 5 – Spring Data JPA & Hibernate
chapter: 9
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Intermediate
estimated_reading_time: 45 Minutes
estimated_practical_time: 60 Minutes
---

# Chapter 9
# Native SQL Queries

> **"JPQL is database-independent, but sometimes you need the full power of SQL. Native Queries allow you to execute database-specific SQL directly from Spring Data JPA."**

---

# 📖 Introduction

In the previous chapter, we learned how to write custom queries using **JPQL**.

JPQL is an excellent choice for most business requirements because it works with Java entities instead of database tables.

However, there are situations where JPQL is not enough.

For example:

- Using database-specific SQL functions
- Writing complex joins
- Executing Common Table Expressions (CTEs)
- Using Window Functions
- Calling Stored Procedures
- Optimising complex reports
- Migrating existing SQL queries

In these cases, Spring Data JPA allows us to execute **Native SQL Queries**.

Unlike JPQL, Native SQL communicates directly with the database.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Native SQL Queries
- Execute SQL using `@Query`
- Use `nativeQuery = true`
- Pass parameters
- Return entities and projections
- Execute update and delete statements
- Understand when Native SQL should be used
- Follow best practices

---

# What is a Native SQL Query?

A Native SQL Query is a standard SQL statement executed directly against the database.

Unlike JPQL:

- SQL uses database tables.
- SQL uses database column names.

Example

```sql
SELECT *
FROM students;
```

This query is executed exactly as written.

---

# JPQL vs Native SQL

JPQL

```java
SELECT s
FROM Student s
```

Native SQL

```sql
SELECT *
FROM students
```

JPQL

- Uses Entity names
- Database independent

Native SQL

- Uses Table names
- Database specific

---

# Writing a Native Query

Spring Data JPA uses the `@Query` annotation.

```java
@Query(
    value = "SELECT * FROM students",
    nativeQuery = true
)
List<Student> findAllStudents();
```

The `nativeQuery = true` attribute tells Spring that the query is plain SQL.

---

# Find Student by Email

```java
@Query(
value = """
SELECT *
FROM students
WHERE email = :email
""",
nativeQuery = true
)
Optional<Student> findByEmail(
        @Param("email") String email);
```

Generated SQL

```sql
SELECT *
FROM students
WHERE email = ?;
```

---

# Using Multiple Conditions

```java
@Query(
value = """
SELECT *
FROM students
WHERE department = :department
AND name = :name
""",
nativeQuery = true
)
List<Student> search(
        @Param("department") String department,
        @Param("name") String name);
```

---

# Sorting Results

```java
@Query(
value = """
SELECT *
FROM students
ORDER BY name ASC
""",
nativeQuery = true
)
List<Student> getStudents();
```

Descending

```sql
ORDER BY name DESC
```

---

# Returning Specific Columns

Instead of retrieving the complete entity:

```sql
SELECT
name,
email
FROM students;
```

The result can be mapped to a projection or DTO.

Example Interface Projection

```java
public interface StudentSummary {

    String getName();

    String getEmail();

}
```

Repository

```java
@Query(
value = """
SELECT
name,
email
FROM students
""",
nativeQuery = true
)
List<StudentSummary> getStudents();
```

Only the required columns are fetched.

---

# Aggregate Queries

Count students.

```java
@Query(
value = """
SELECT COUNT(*)
FROM students
""",
nativeQuery = true
)
long totalStudents();
```

Average

```sql
SELECT AVG(id)
FROM students;
```

Maximum

```sql
SELECT MAX(id)
FROM students;
```

Minimum

```sql
SELECT MIN(id)
FROM students;
```

---

# Update Query

Updating records requires:

- `@Modifying`
- `@Transactional`

```java
@Modifying
@Transactional
@Query(
value = """
UPDATE students
SET department = :department
WHERE id = :id
""",
nativeQuery = true
)
void updateDepartment(
        Long id,
        String department);
```

---

# Delete Query

```java
@Modifying
@Transactional
@Query(
value = """
DELETE
FROM students
WHERE id = :id
""",
nativeQuery = true
)
void deleteStudent(Long id);
```

---

# LIKE Query

```java
@Query(
value = """
SELECT *
FROM students
WHERE name LIKE %:keyword%
""",
nativeQuery = true
)
List<Student> search(
        String keyword);
```

---

# LIMIT Query

Retrieve the latest five students.

```java
@Query(
value = """
SELECT *
FROM students
ORDER BY id DESC
LIMIT 5
""",
nativeQuery = true
)
List<Student> latestStudents();
```

> **Note:** `LIMIT` is supported by databases such as MySQL and PostgreSQL but not by every relational database.

---

# Calling Database Functions

Example (MySQL)

```sql
SELECT
UPPER(name)
FROM students;
```

Repository

```java
@Query(
value = """
SELECT
UPPER(name)
FROM students
""",
nativeQuery = true
)
List<String> names();
```

---

# Calling Stored Procedures

Some enterprise applications use stored procedures.

Example SQL

```sql
CALL get_all_students();
```

Repository

```java
@Query(
value = "CALL get_all_students()",
nativeQuery = true
)
List<Student> getStudents();
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

Native SQL

↓

Database
```

Unlike JPQL, Hibernate does not translate the query. It sends the SQL directly to the database.

---

# Advantages of Native SQL

- Uses full SQL features
- Database-specific optimisation
- Supports advanced SQL functions
- Supports stored procedures
- Better for complex reporting
- Easier migration of existing SQL

---

# Limitations

Native SQL:

- Is database dependent
- Is harder to migrate between databases
- Uses table and column names directly
- Can become difficult to maintain if overused

---

# JPQL or Native SQL?

| JPQL | Native SQL |
|------|------------|
| Database independent | Database specific |
| Uses entities | Uses tables |
| Easier maintenance | More SQL flexibility |
| Best for business queries | Best for advanced SQL |
| Portable | Vendor dependent |

---

# When Should You Use Native SQL?

Use Native SQL when:

- Existing SQL already exists.
- Database-specific features are required.
- Complex joins are difficult in JPQL.
- Window functions or CTEs are needed.
- Performance tuning requires vendor-specific SQL.

For standard CRUD and filtering, prefer JPQL or Derived Query Methods.

---

# Best Practices

- Use Native SQL only when JPQL cannot solve the problem.
- Keep SQL readable.
- Use named parameters.
- Return projections when possible.
- Test queries against the target database.

---

# Common Mistakes

❌ Using Native SQL for simple CRUD operations.

❌ Forgetting `nativeQuery = true`.

❌ Forgetting `@Modifying` for update and delete queries.

❌ Returning entire entities when only a few columns are required.

❌ Writing database-specific SQL without considering portability.

---

# Industry Insight

Most enterprise projects use a combination of query techniques.

Typical usage:

- **Derived Query Methods** → Simple lookups
- **JPQL** → Business filtering and joins
- **Native SQL** → Reports, analytics, database-specific optimisations, and legacy SQL migration

Choosing the right approach keeps the application both maintainable and performant.

---

# 🧪 Hands-on Lab

## Objective

Implement Native SQL queries for the Student Management System.

### Tasks

1. Retrieve all students using SQL.
2. Search students by email.
3. Count total students.
4. Update a department using SQL.
5. Delete a student using SQL.
6. Create a projection for name and email.
7. Test each query using Postman.

---

# 💼 Interview Corner

### Q1. What is a Native SQL Query?

A Native SQL Query is a standard SQL statement executed directly against the database.

---

### Q2. What does `nativeQuery = true` do?

It tells Spring Data JPA that the query is plain SQL instead of JPQL.

---

### Q3. When should Native SQL be preferred over JPQL?

When database-specific features, advanced SQL, stored procedures, or performance tuning are required.

---

### Q4. Does Native SQL use entity names?

No. It uses database table and column names.

---

### Q5. What annotations are required for update and delete queries?

```java
@Modifying
@Transactional
```

---

# 📄 Cheat Sheet

| Feature | Example |
|---------|---------|
| Native Query | `@Query(..., nativeQuery = true)` |
| Find All | `SELECT * FROM students` |
| Filter | `WHERE department = :department` |
| Sort | `ORDER BY name ASC` |
| Count | `SELECT COUNT(*)` |
| Update | `UPDATE students SET ...` |
| Delete | `DELETE FROM students` |
| Stored Procedure | `CALL procedure_name()` |
| Projection | Return interface or DTO |

---

# 📝 Chapter Summary

In this chapter, you learned how to execute **Native SQL Queries** using Spring Data JPA. You explored the `@Query` annotation with `nativeQuery = true`, executed select, update, and delete operations, returned projections, used aggregate functions, and called stored procedures.

Native SQL provides maximum flexibility and performance when JPQL is insufficient, but it should be used carefully because it is tied to the underlying database.

---

# 🚀 What's Next?

In **Chapter 10 – Entity Relationships**, you'll learn how to model real-world relationships such as **One-to-One**, **One-to-Many**, **Many-to-One**, and **Many-to-Many**, along with concepts like cascading, fetch strategies, and owning vs. inverse sides of relationships.
