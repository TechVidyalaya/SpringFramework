
---
title: Configuring Spring Data JPA
module: Module 5 – Spring Data JPA & Hibernate
chapter: 3
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Beginner
estimated_reading_time: 35 Minutes
estimated_practical_time: 45 Minutes
---

# Chapter 3
# Configuring Spring Data JPA

> **"Before Hibernate can manage your entities, your application must know how to connect to the database."**

---

# 📖 Introduction

In the previous chapter, we learned how Hibernate maps Java objects to database tables using ORM.

In this chapter, we'll configure Spring Boot to connect to a database using **Spring Data JPA**. We'll use the embedded **H2 Database** for learning and also see how to connect to **MySQL** and **PostgreSQL**, which are commonly used in production.

By the end of this chapter, your Student Management application will be ready to store data permanently.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Add Spring Data JPA to a Spring Boot project
- Configure H2 Database
- Configure MySQL
- Configure PostgreSQL
- Understand important JPA properties
- Enable SQL logging
- Verify database connectivity

---

# Required Dependencies

Add the following dependencies to your `pom.xml`.

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>

</dependencies>
```

The JPA starter automatically includes Hibernate.

---

# Why Use H2 Database?

H2 is an **in-memory database** designed for development and testing.

Advantages:

- Lightweight
- No installation required
- Fast startup
- Ideal for learning
- Easy integration with Spring Boot

---

# H2 Configuration

Configure `application.properties`.

```properties
spring.datasource.url=jdbc:h2:mem:studentdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

---

# Accessing the H2 Console

Enable the H2 console.

```properties
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

Open your browser.

```text
http://localhost:8080/h2-console
```

---

# MySQL Configuration

For production, MySQL is one of the most popular databases.

Dependency:

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>
```

Configuration:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/studentdb
spring.datasource.username=root
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

# PostgreSQL Configuration

Dependency:

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
```

Configuration:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/studentdb
spring.datasource.username=postgres
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update
```

---

# Understanding Important JPA Properties

## `spring.jpa.hibernate.ddl-auto`

Controls how Hibernate manages database tables.

| Value | Description |
|--------|-------------|
| none | No schema changes |
| validate | Validate existing tables |
| update | Update schema without deleting data |
| create | Create new tables every startup |
| create-drop | Create and delete tables when application stops |

For development:

```properties
spring.jpa.hibernate.ddl-auto=update
```

---

## `spring.jpa.show-sql`

Displays generated SQL.

```properties
spring.jpa.show-sql=true
```

Example:

```sql
select * from students;
```

---

## Format SQL

```properties
spring.jpa.properties.hibernate.format_sql=true
```

Makes SQL easier to read.

---

# DataSource

A **DataSource** manages database connections.

```text
Application

↓

DataSource

↓

Database
```

Spring Boot automatically creates the DataSource using the properties in `application.properties`.

---

# SQL Logging

Enable detailed Hibernate logs.

```properties
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.orm.jdbc.bind=TRACE
```

Example output:

```sql
select
    *
from
    students
where
    id=?
```

---

# Student Management Project Setup

Our application architecture is now:

```text
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

Although we haven't created entities yet, the project is now ready for database communication.

---

# Verify the Configuration

Run the application.

If everything is configured correctly:

- Application starts successfully.
- No database connection errors.
- H2 Console opens.
- Hibernate creates the schema.

---

# Common Configuration Errors

## Database Not Running

```
Connection refused
```

Solution:

Start the database server.

---

## Wrong Username or Password

```
Access denied
```

Solution:

Verify database credentials.

---

## Driver Not Found

```
No suitable driver found
```

Solution:

Add the correct database dependency.

---

# Best Practices

- Use H2 for learning.
- Use MySQL or PostgreSQL in production.
- Never store passwords in source code.
- Enable SQL logging during development only.
- Use `update` during development and `validate` in production.

---

# 🧪 Hands-on Lab

## Objective

Configure Spring Boot to connect to an H2 database.

### Tasks

1. Add Spring Data JPA dependency.
2. Add H2 dependency.
3. Configure `application.properties`.
4. Enable the H2 console.
5. Run the application.
6. Open the H2 console.
7. Verify that the application connects successfully.

---

# 💼 Interview Corner

### Q1. What is Spring Data JPA?

Spring Data JPA simplifies database access by providing repository interfaces built on top of JPA.

---

### Q2. Why is H2 used?

H2 is an embedded database used for development and testing.

---

### Q3. What does `ddl-auto=update` do?

It automatically updates the database schema without deleting existing data.

---

### Q4. How can you see generated SQL?

By enabling:

```properties
spring.jpa.show-sql=true
```

---

### Q5. Which databases are commonly used with Spring Boot?

- MySQL
- PostgreSQL
- Oracle
- SQL Server
- H2

---

# 📄 Cheat Sheet

| Property | Purpose |
|----------|---------|
| `spring.datasource.url` | Database URL |
| `spring.datasource.username` | Database username |
| `spring.datasource.password` | Database password |
| `spring.jpa.hibernate.ddl-auto` | Schema management |
| `spring.jpa.show-sql` | Display SQL |
| `spring.jpa.properties.hibernate.format_sql` | Format SQL output |
| `spring.h2.console.enabled` | Enable H2 Console |

---

# 📝 Chapter Summary

In this chapter, you configured Spring Boot to use Spring Data JPA with H2, MySQL, and PostgreSQL. You learned how to add the required dependencies, configure datasource properties, enable SQL logging, and understand key Hibernate configuration options such as `ddl-auto`.

Your project is now fully prepared to communicate with a database.

---

# 🚀 What's Next?

In **Chapter 4 – Entity Mapping**, you'll create your first JPA entity using annotations like `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, and `@Column`, and map Java objects to database tables.
