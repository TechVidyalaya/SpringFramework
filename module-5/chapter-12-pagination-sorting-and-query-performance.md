---
title: Pagination, Sorting, and Query Performance
module: Module 5 – Spring Data JPA & Hibernate
chapter: 12
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Intermediate
estimated_reading_time: 55 Minutes
estimated_practical_time: 90 Minutes
---

# Chapter 12
# Pagination, Sorting, and Query Performance

> **"Efficient applications don't load everything—they load only what the user needs, when the user needs it."**

---

# 📖 Introduction

Imagine your Student Management System has only 20 students.

Loading all students at once is easy.

Now imagine your application has:

- 500,000 students
- 10 million customers
- 100 million orders

Should your application load all records into memory?

**Absolutely not.**

Loading unnecessary data:

- Increases response time
- Consumes more memory
- Slows the database
- Creates poor user experience

Spring Data JPA solves this problem using:

- Pagination
- Sorting
- Efficient query execution

In this chapter, you'll learn how enterprise applications retrieve only the required data while maintaining high performance.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand pagination
- Implement paging using `Pageable`
- Sort query results
- Understand `Page` and `Slice`
- Improve query performance
- Avoid the N+1 Query Problem
- Optimise database access
- Follow JPA performance best practices

---

# Why Pagination?

Suppose the database contains:

```text
Students

----------------

1

2

3

...

1,000,000

----------------
```

Loading every record means:

```text
Database

↓

1,000,000 Records

↓

Application

↓

Browser
```

This wastes:

- Memory
- CPU
- Network bandwidth

Instead, retrieve only the required page.

---

# Pagination Concept

```text
Page 1

Records 1–10

↓

Page 2

Records 11–20

↓

Page 3

Records 21–30
```

Each request loads only a small subset of data.

---

# Pageable Interface

Spring Data JPA provides the `Pageable` interface.

Repository

```java
Page<Student> findAll(Pageable pageable);
```

No implementation is required.

---

# Creating a Pageable Object

```java
Pageable pageable =
        PageRequest.of(0, 10);
```

Meaning:

```text
Page Number

0

Page Size

10
```

Remember:

Page numbers start from **0**.

---

# Fetching a Page

```java
Page<Student> students =
        repository.findAll(pageable);
```

This retrieves:

```text
Students

1–10
```

instead of the entire table.

---

# Page Object

The `Page` interface contains both data and metadata.

```java
Page<Student> page =
        repository.findAll(pageable);
```

Useful methods:

```java
page.getContent();

page.getTotalElements();

page.getTotalPages();

page.getNumber();

page.getSize();

page.hasNext();

page.hasPrevious();
```

---

# Pagination Response

Example

```text
Page Number

2

Total Pages

20

Total Records

200

Current Records

10
```

Perfect for REST APIs.

---

# Page vs Slice

Spring Data JPA provides two paging options.

### Page

```java
Page<Student>
```

Contains:

- Data
- Total records
- Total pages

---

### Slice

```java
Slice<Student>
```

Contains:

- Data
- Next page information

Does **not** calculate the total number of records.

---

# When to Use Page or Slice?

| Page | Slice |
|------|-------|
| Includes total count | No total count |
| Slightly slower | Faster |
| Good for dashboards | Good for infinite scrolling |
| Suitable for reports | Suitable for mobile applications |

---

# Sorting

Sorting arranges records in a specific order.

Examples:

```text
Name

A → Z
```

```text
Date

Newest → Oldest
```

---

# Creating Sort

Ascending

```java
Sort sort =
        Sort.by("name");
```

Descending

```java
Sort sort =
        Sort.by(
            Sort.Direction.DESC,
            "name");
```

---

# Sorting with Pagination

```java
Pageable pageable =
        PageRequest.of(
            0,
            10,
            Sort.by("name"));
```

Descending

```java
PageRequest.of(
0,
10,
Sort.by(
Sort.Direction.DESC,
"name"));
```

---

# Multiple Sorting

Sort by department first,

then by name.

```java
Sort.by("department")
    .and(Sort.by("name"));
```

---

# Service Example

```java
public Page<Student> getStudents(
        int page,
        int size) {

    Pageable pageable =
            PageRequest.of(page, size);

    return repository.findAll(pageable);

}
```

---

# REST API Example

```http
GET /students?page=0&size=10
```

Sorted

```http
GET /students?page=0&size=10&sort=name
```

Descending

```http
GET /students?page=0&size=10&sort=name,desc
```

---

# SQL Generated

Hibernate generates SQL similar to:

```sql
SELECT *
FROM students
ORDER BY name
LIMIT 10
OFFSET 0;
```

The exact syntax depends on the database.

---

# Query Performance

Performance depends on:

- Query complexity
- Database indexes
- Number of records
- Network latency
- Fetch strategy

---

# Database Indexes

Without an index

```text
Search

↓

Record 900000

↓

Scan Entire Table
```

With an index

```text
Index

↓

Direct Lookup

↓

Record Found
```

Indexes dramatically improve search performance.

Example

```sql
CREATE INDEX idx_email
ON students(email);
```

---

# Select Only Required Columns

Avoid

```java
SELECT s
FROM Student s
```

if only the student's name is needed.

Prefer

```java
SELECT s.name
FROM Student s
```

Or return a DTO.

This reduces memory usage and network traffic.

---

# The N+1 Query Problem

Suppose each student belongs to a department.

```text
Load Students

↓

1 Query
```

Then

```text
Load Department
```

for every student.

```text
Student 1

↓

Department

Student 2

↓

Department

Student 3

↓

Department
```

Total

```text
1 + N Queries
```

This is known as the **N+1 Query Problem**.

---

# Example

```java
List<Student> students =
        repository.findAll();
```

Later

```java
student.getDepartment().getName();
```

Hibernate executes additional queries for each department.

---

# Solving the N+1 Problem

Use `JOIN FETCH`.

```java
@Query("""
SELECT s
FROM Student s
JOIN FETCH s.department
""")
List<Student> getStudents();
```

Now:

```text
One Query

↓

Students

+

Departments
```

---

# Entity Graph

Another solution is using `@EntityGraph`.

```java
@EntityGraph(
attributePaths = "department"
)
List<Student> findAll();
```

This tells Hibernate to load the related department efficiently.

---

# Batch Fetching

Hibernate can fetch related entities in batches.

```properties
spring.jpa.properties.hibernate.default_batch_fetch_size=20
```

Instead of executing 100 separate queries,

Hibernate retrieves related entities in batches.

---

# Lazy Loading

Prefer

```java
FetchType.LAZY
```

instead of

```java
FetchType.EAGER
```

Load related entities only when required.

---

# Monitoring SQL

Enable SQL logging.

```properties
spring.jpa.show-sql=true
```

Better formatting

```properties
spring.jpa.properties.hibernate.format_sql=true
```

This helps identify inefficient queries.

---

# Performance Checklist

Before deploying an application, verify:

- Pagination is implemented.
- Queries return only required fields.
- Frequently searched columns are indexed.
- Lazy loading is used appropriately.
- N+1 queries are eliminated.
- SQL logs have been reviewed.

---

# Best Practices

- Always paginate large datasets.
- Use sorting instead of sorting in Java.
- Return DTOs for read-only APIs.
- Use indexes for frequently searched columns.
- Prefer `JOIN FETCH` or `@EntityGraph` when appropriate.
- Avoid unnecessary eager loading.

---

# Common Mistakes

❌ Loading all records using `findAll()`.

❌ Returning entire entities when only a few fields are needed.

❌ Forgetting database indexes.

❌ Ignoring the N+1 Query Problem.

❌ Using `FetchType.EAGER` everywhere.

---

# Industry Insight

Pagination and query optimisation are essential in applications such as:

- Amazon
- Netflix
- LinkedIn
- Banking systems
- ERP software
- Hospital Management Systems

Without pagination, even modern servers can struggle under heavy load. Performance optimisation is therefore a critical responsibility for backend developers.

---

# 🧪 Hands-on Lab

## Objective

Implement efficient data retrieval.

### Tasks

1. Implement pagination using `PageRequest`.
2. Add sorting by student name.
3. Add descending sorting by ID.
4. Create an endpoint supporting `page` and `size`.
5. Use DTO projection for student summaries.
6. Enable SQL logging.
7. Observe SQL queries.
8. Optimise a relationship using `JOIN FETCH`.

---

# 💼 Interview Corner

### Q1. What is pagination?

Pagination divides a large dataset into smaller pages so that only a subset of records is retrieved at a time.

---

### Q2. What is the difference between `Page` and `Slice`?

`Page` includes total record and page counts, while `Slice` retrieves only enough information to determine whether another page exists.

---

### Q3. What is the N+1 Query Problem?

It occurs when one query retrieves parent entities and additional queries are executed for each related entity, leading to excessive database calls.

---

### Q4. How can the N+1 Query Problem be solved?

Using techniques such as:

- `JOIN FETCH`
- `@EntityGraph`
- Batch fetching
- Appropriate fetch strategies

---

### Q5. Why are indexes important?

Indexes reduce the amount of data the database scans, making search and filtering operations much faster.

---

# 📄 Cheat Sheet

| Feature | Purpose |
|---------|---------|
| `Pageable` | Pagination information |
| `PageRequest.of()` | Create pagination |
| `Page` | Data + metadata |
| `Slice` | Lightweight pagination |
| `Sort.by()` | Sort records |
| `JOIN FETCH` | Load relationships in one query |
| `@EntityGraph` | Optimise fetching |
| `default_batch_fetch_size` | Batch load related entities |
| Database Index | Speed up searches |

---

# 📝 Chapter Summary

In this chapter, you learned how to efficiently retrieve large datasets using pagination and sorting. You explored the `Pageable`, `Page`, and `Slice` interfaces, implemented dynamic sorting, and learned how Hibernate generates paginated SQL queries.

You also discovered key performance optimisation techniques, including indexing, DTO projections, `JOIN FETCH`, `@EntityGraph`, batch fetching, and avoiding the N+1 Query Problem. These techniques are widely used in enterprise applications to improve scalability and responsiveness.

---

# 🚀 What's Next?

In **Chapter 13 – Projections, Specifications, and Query by Example**, you'll learn advanced querying techniques that allow you to retrieve only the required data, build dynamic search filters without writing multiple query methods, and create flexible search APIs suitable for enterprise applications.
