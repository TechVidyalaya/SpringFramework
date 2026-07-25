
---
title: Projections, Specifications, and Query by Example
module: Module 5 – Spring Data JPA & Hibernate
chapter: 13
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Advanced
estimated_reading_time: 60 Minutes
estimated_practical_time: 90 Minutes
---

# Chapter 13
# Projections, Specifications, and Query by Example

> **"Enterprise applications rarely search using fixed conditions. Users expect dynamic filtering, flexible searches, and efficient data retrieval. Spring Data JPA provides powerful features to achieve this without writing complex SQL."**

---

# 📖 Introduction

Imagine you're building a Student Management System.

Users may search by:

- Name
- Email
- Department
- Age
- City
- Admission Date

Sometimes they search using only one field.

Sometimes they combine multiple filters.

Sometimes they leave some fields empty.

Should we create repository methods like these?

```java
findByName()

findByDepartment()

findByDepartmentAndName()

findByDepartmentAndNameAndCity()

findByDepartmentAndNameAndCityAndEmail()
```

This quickly becomes impossible to maintain.

Spring Data JPA solves this problem using three powerful features:

- **Projections**
- **Specifications**
- **Query by Example (QBE)**

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand projections
- Create interface-based projections
- Create DTO projections
- Build dynamic queries using Specifications
- Use the Criteria API
- Implement Query by Example (QBE)
- Decide when to use each technique
- Follow enterprise best practices

---

# Why Do We Need These Features?

Without advanced querying:

```text
Repository

↓

Hundreds of Methods

↓

Difficult Maintenance
```

With Spring Data JPA:

```text
Repository

↓

Dynamic Queries

↓

Cleaner Code
```

---

# Part 1 – Projections

---

# What is a Projection?

A Projection retrieves only the fields you need.

Instead of returning:

```text
Student

id

name

email

department

address

phone

dateOfBirth

createdAt
```

Suppose the UI only needs:

```text
Name

Email
```

Why load everything?

---

# Benefits of Projections

- Faster queries
- Less memory usage
- Smaller API responses
- Better performance
- Cleaner APIs

---

# Interface-Based Projection

Create an interface.

```java
public interface StudentSummary {

    String getName();

    String getEmail();

}
```

Repository

```java
List<StudentSummary> findByDepartment(
        String department);
```

Spring automatically maps the result.

---

# Generated SQL

```sql
SELECT
name,
email
FROM students
WHERE department = ?;
```

Only the required columns are retrieved.

---

# DTO Projection

Create a DTO.

```java
public class StudentResponse {

    private String name;

    private String email;

    public StudentResponse(
            String name,
            String email) {

        this.name = name;
        this.email = email;

    }

}
```

Repository

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

---

# Projection Comparison

| Interface Projection | DTO Projection |
|----------------------|----------------|
| No constructor | Constructor required |
| Less code | More control |
| Simple queries | Complex business responses |
| Read-only | Can include custom logic |

---

# Part 2 – Specifications

---

# What is a Specification?

A Specification builds queries dynamically.

Example

User enters

```text
Department

Computer Science
```

No name.

Later another user enters

```text
Department

Computer Science

Name

Rahul
```

Instead of writing multiple repository methods,

we build the query dynamically.

---

# Enable Specifications

Repository

```java
public interface StudentRepository
extends JpaRepository<Student, Long>,
JpaSpecificationExecutor<Student> {

}
```

Now the repository supports Specifications.

---

# Creating a Specification

```java
public class StudentSpecification {

    public static Specification<Student>
    hasDepartment(String department) {

        return (root, query, cb) ->
                cb.equal(
                    root.get("department"),
                    department);

    }

}
```

---

# Searching with Specification

```java
List<Student> students =
repository.findAll(
StudentSpecification.hasDepartment(
"Computer Science"));
```

---

# Combining Specifications

```java
Specification<Student> specification =
Specification
.where(hasDepartment("Computer Science"))
.and(hasName("Rahul"));
```

Spring combines the conditions.

Equivalent SQL

```sql
WHERE department = ?

AND name = ?
```

---

# OR Condition

```java
Specification
.where(hasDepartment("IT"))
.or(hasDepartment("CSE"));
```

---

# Dynamic Search

Suppose the search form contains:

```text
Name

Department

City

Email
```

Only the entered values become part of the query.

This avoids creating dozens of repository methods.

---

# Criteria API

Specifications use the JPA Criteria API internally.

Main components:

```text
Root

↓

CriteriaQuery

↓

CriteriaBuilder
```

Example

```java
(root, query, builder) ->
builder.equal(
root.get("email"),
email);
```

Spring converts this into SQL automatically.

---

# Part 3 – Query by Example (QBE)

---

# What is Query by Example?

Instead of writing a query,

create an example object.

Spring searches using its non-null fields.

---

# Creating an Example

```java
Student student = new Student();

student.setDepartment(
"Computer Science");
```

Create an Example.

```java
Example<Student> example =
Example.of(student);
```

Execute

```java
repository.findAll(example);
```

Generated SQL

```sql
WHERE department = ?
```

---

# Multiple Fields

```java
Student student = new Student();

student.setDepartment("IT");

student.setName("Rahul");
```

Spring generates

```sql
WHERE department = ?

AND name = ?
```

---

# ExampleMatcher

Customise matching behaviour.

```java
ExampleMatcher matcher =
ExampleMatcher.matching()
.withIgnoreCase()
.withStringMatcher(
ExampleMatcher.StringMatcher.CONTAINING);
```

Create the Example

```java
Example<Student> example =
Example.of(student, matcher);
```

Now searches become:

```text
Case-insensitive

Contains matching
```

---

# Query by Example Flow

```text
Example Object

↓

ExampleMatcher

↓

Repository

↓

Generated SQL
```

---

# Choosing the Right Approach

| Feature | Best For |
|----------|-----------|
| Derived Query | Simple searches |
| JPQL | Custom business queries |
| Native SQL | Database-specific SQL |
| Projection | Retrieve selected fields |
| Specification | Dynamic filtering |
| Query by Example | Form-based searching |

---

# Performance Comparison

| Feature | Performance | Flexibility |
|----------|-------------|-------------|
| Projection | Excellent | Medium |
| Specification | Good | Excellent |
| QBE | Good | Medium |

---

# Student Search API

User Request

```text
Department = IT

Name = Rahul

Email = Empty
```

Specification automatically generates

```sql
WHERE department = ?

AND name = ?
```

No unnecessary conditions are added.

---

# Best Practices

- Use projections for read-only APIs.
- Use Specifications for advanced filtering.
- Use QBE for simple search forms.
- Keep specifications reusable.
- Return DTOs instead of entities where appropriate.

---

# Common Mistakes

❌ Creating dozens of repository methods.

❌ Returning complete entities when only a few fields are required.

❌ Using QBE for highly complex queries.

❌ Writing Specifications for very simple searches.

❌ Duplicating filtering logic across services.

---

# Industry Insight

Large enterprise applications rarely rely only on Derived Query Methods.

Typical usage:

| Scenario | Solution |
|----------|----------|
| Search screen | Specification |
| Dashboard | Projection |
| Admin filters | Specification |
| Profile summary | Projection |
| Basic search form | Query by Example |

This combination keeps repositories clean while supporting complex business requirements.

---

# 🧪 Hands-on Lab

## Objective

Build a flexible search module.

### Tasks

1. Create `StudentSummary` interface projection.
2. Create `StudentResponse` DTO projection.
3. Enable `JpaSpecificationExecutor`.
4. Create specifications for:
   - Name
   - Department
   - Email
5. Combine specifications dynamically.
6. Implement Query by Example.
7. Compare the results of all three approaches.

---

# 💼 Interview Corner

### Q1. What is a Projection?

A Projection retrieves only the required fields instead of loading the complete entity.

---

### Q2. What is the difference between Interface Projection and DTO Projection?

Interface projections are simpler and generated automatically by Spring, while DTO projections provide greater flexibility through constructors and custom logic.

---

### Q3. What is a Specification?

A Specification is a reusable predicate that builds dynamic database queries using the JPA Criteria API.

---

### Q4. What is Query by Example?

Query by Example allows searches using an example entity where all non-null fields become search criteria automatically.

---

### Q5. When should Specifications be preferred?

Specifications are ideal when users can combine multiple optional search filters and the query needs to be built dynamically.

---

# 📄 Cheat Sheet

| Feature | Purpose |
|---------|---------|
| Interface Projection | Retrieve selected fields |
| DTO Projection | Custom response objects |
| `JpaSpecificationExecutor` | Enable Specifications |
| `Specification.where()` | Start a dynamic query |
| `.and()` | Combine conditions with AND |
| `.or()` | Combine conditions with OR |
| `Example.of()` | Create a Query by Example |
| `ExampleMatcher` | Customise matching behaviour |

---

# 📝 Chapter Summary

In this chapter, you explored three advanced Spring Data JPA features that simplify enterprise querying. You learned how **Projections** improve performance by retrieving only the required fields, how **Specifications** build flexible and reusable dynamic queries, and how **Query by Example** provides an easy way to implement form-based searches without writing custom SQL.

These techniques are widely used in enterprise applications to build scalable, maintainable, and high-performance search functionality.

---

# 🚀 What's Next?

In **Chapter 14 – Auditing, Caching, and Optimistic Locking**, you'll learn how to automatically track entity creation and updates, improve application performance using caching, and prevent data conflicts when multiple users modify the same record simultaneously.
