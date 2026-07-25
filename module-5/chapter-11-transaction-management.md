---
title: Transaction Management
module: Module 5 – Spring Data JPA & Hibernate
chapter: 11
course: Industry Ready Java Developer
author: TechVidyalaya
version: 1.0
difficulty: Intermediate
estimated_reading_time: 50 Minutes
estimated_practical_time: 75 Minutes
---

# Chapter 11
# Transaction Management

> **"A transaction ensures that a group of database operations either completes successfully as a whole or does not happen at all."**

---

# 📖 Introduction

Imagine you're transferring money between two bank accounts.

The process involves two operations:

1. Debit money from Account A.
2. Credit money to Account B.

What happens if the debit succeeds but the credit fails because of a server crash?

```text
Account A : £900

Account B : £500
```

The customer loses money, and the database becomes inconsistent.

To prevent such situations, databases use **Transactions**.

A transaction groups multiple operations into a single unit of work.

If every operation succeeds, the transaction is **committed**.

If any operation fails, the transaction is **rolled back**, and the database returns to its previous state.

Spring Boot simplifies transaction management using the `@Transactional` annotation.

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand database transactions
- Learn ACID properties
- Use the `@Transactional` annotation
- Commit and rollback transactions
- Handle exceptions correctly
- Configure transaction propagation
- Configure transaction isolation
- Follow transaction management best practices

---

# What is a Transaction?

A transaction is a sequence of database operations executed as a single unit.

Example:

```text
Update Account A

↓

Update Account B

↓

Commit
```

If one operation fails,

```text
Rollback
```

---

# Transaction Example

Without Transaction

```text
Debit

✓

Credit

✗
```

Result

```text
Money Lost
```

With Transaction

```text
Debit

↓

Credit

↓

Commit
```

If Credit fails

```text
Rollback

↓

Original Balance Restored
```

---

# ACID Properties

Every database transaction follows the **ACID** principles.

| Property | Meaning |
|----------|---------|
| Atomicity | All operations succeed or none succeed |
| Consistency | Database remains valid before and after the transaction |
| Isolation | Transactions do not interfere with one another |
| Durability | Committed data is permanently stored |

---

# Atomicity

A transaction is **all or nothing**.

Example

```text
Debit

↓

Credit
```

If Credit fails

```text
Rollback
```

No partial updates are saved.

---

# Consistency

The database must always remain in a valid state.

Example

Before transfer

```text
Account A : £1000

Account B : £500
```

After transfer

```text
Account A : £900

Account B : £600
```

The total balance remains consistent.

---

# Isolation

Multiple users may access the database simultaneously.

```text
User A

↓

Database

↑

User B
```

Isolation prevents transactions from corrupting each other's data.

---

# Durability

Once a transaction is committed, the changes are permanently stored.

Even if:

- The application crashes
- The server restarts
- The network disconnects

Committed data remains safe.

---

# Using @Transactional

Spring Boot manages transactions with the `@Transactional` annotation.

```java
@Service
public class StudentService {

    @Transactional
    public void registerStudent(Student student) {

        repository.save(student);

    }

}
```

Spring automatically begins and commits the transaction.

---

# Multiple Database Operations

Example

```java
@Transactional
public void registerStudent(Student student) {

    studentRepository.save(student);

    courseRepository.save(course);

}
```

Both operations succeed together.

If either operation fails,

Spring rolls back the transaction.

---

# Transaction Lifecycle

```text
Transaction Starts

↓

SQL Operations

↓

Commit

OR

Rollback
```

---

# Commit

If no exception occurs,

```text
Transaction

↓

Commit

↓

Data Saved
```

---

# Rollback

If an exception occurs,

```text
Transaction

↓

Rollback

↓

Changes Discarded
```

---

# Example with Exception

```java
@Transactional
public void saveStudent(Student student) {

    repository.save(student);

    if (true) {

        throw new RuntimeException();

    }

}
```

Result

```text
Rollback
```

No record is inserted into the database.

---

# Checked vs Unchecked Exceptions

By default, Spring rolls back transactions for:

- `RuntimeException`
- `Error`

It does **not** roll back for checked exceptions unless configured.

Example

```java
@Transactional(
rollbackFor = Exception.class
)
```

Now all exceptions trigger a rollback.

---

# Propagation

Propagation determines how nested transactions behave.

```text
Service A

↓

Service B
```

Should Service B:

- Join the existing transaction?
- Start a new transaction?

The propagation setting controls this behaviour.

---

# Common Propagation Types

| Propagation | Description |
|------------|-------------|
| REQUIRED | Join existing transaction or create a new one |
| REQUIRES_NEW | Always create a new transaction |
| SUPPORTS | Join if one exists |
| NOT_SUPPORTED | Execute without a transaction |
| NEVER | Throw an exception if a transaction exists |
| MANDATORY | Require an existing transaction |

---

# REQUIRED (Default)

```java
@Transactional(
propagation =
Propagation.REQUIRED
)
```

Behaviour

```text
Existing Transaction

↓

Join

OR

Create New
```

This is the default and most commonly used option.

---

# REQUIRES_NEW

```java
@Transactional(
propagation =
Propagation.REQUIRES_NEW
)
```

Always starts a completely new transaction.

Useful for:

- Audit logging
- Notification history
- Independent database updates

---

# Isolation Levels

Isolation determines how transactions interact with each other.

---

# Common Isolation Levels

| Isolation | Description |
|-----------|-------------|
| DEFAULT | Uses the database default |
| READ_UNCOMMITTED | Lowest isolation level |
| READ_COMMITTED | Prevents dirty reads |
| REPEATABLE_READ | Prevents non-repeatable reads |
| SERIALIZABLE | Highest isolation level |

---

# Dirty Read

Transaction A

```text
Update Salary

↓

Not Committed
```

Transaction B

```text
Reads Updated Salary
```

If Transaction A rolls back,

Transaction B has read invalid data.

`READ_COMMITTED` prevents this problem.

---

# Setting Isolation

```java
@Transactional(
isolation =
Isolation.READ_COMMITTED
)
public void updateStudent() {

}
```

---

# Read-Only Transactions

If data is only being retrieved,

mark the transaction as read-only.

```java
@Transactional(readOnly = true)
public List<Student> getStudents() {

    return repository.findAll();

}
```

Benefits:

- Better performance
- Reduced locking
- Clear intent

---

# Transaction Timeout

Prevent long-running transactions.

```java
@Transactional(timeout = 10)
```

If the transaction exceeds 10 seconds,

Spring rolls it back.

---

# Transaction Flow

```text
Controller

↓

Service

↓

@Transactional

↓

Repository

↓

Database
```

---

# Best Practices

- Keep transactions short.
- Place `@Transactional` in the Service Layer.
- Avoid network calls inside transactions.
- Use `readOnly = true` for read operations.
- Handle exceptions carefully.
- Prefer the default `REQUIRED` propagation unless another behaviour is required.

---

# Common Mistakes

❌ Placing `@Transactional` on repository interfaces unnecessarily.

❌ Performing long-running business logic inside transactions.

❌ Ignoring rollback behaviour for checked exceptions.

❌ Using `EAGER` database operations inside long transactions.

❌ Calling private methods expecting transaction management (Spring proxies only intercept eligible public method calls from outside the bean).

---

# Industry Insight

Transaction management is critical in systems where data consistency is essential.

Examples include:

| Domain | Transaction Example |
|---------|---------------------|
| Banking | Money transfers |
| E-Commerce | Order placement and payment |
| Healthcare | Patient admission and billing |
| Airline | Seat booking |
| Inventory | Stock updates |
| Payroll | Salary processing |

A poorly designed transaction can lead to data corruption, deadlocks, or performance issues.

---

# 🧪 Hands-on Lab

## Objective

Implement transactional operations in the Student Management System.

### Tasks

1. Create a service method annotated with `@Transactional`.
2. Save a new student.
3. Save an enrolment record.
4. Throw a `RuntimeException`.
5. Verify that both operations are rolled back.
6. Create a read-only method using `@Transactional(readOnly = true)`.
7. Experiment with different propagation settings.

---

# 💼 Interview Corner

### Q1. What is a transaction?

A transaction is a group of database operations executed as a single unit of work. All operations either succeed together or fail together.

---

### Q2. What are the ACID properties?

- **Atomicity**
- **Consistency**
- **Isolation**
- **Durability**

These properties ensure reliable transaction processing.

---

### Q3. What does `@Transactional` do?

It tells Spring to automatically begin, commit, or roll back a database transaction around the annotated method.

---

### Q4. When does Spring roll back a transaction by default?

By default, Spring rolls back transactions when an unchecked exception (`RuntimeException`) or an `Error` is thrown.

---

### Q5. Why should `@Transactional(readOnly = true)` be used?

It optimises read operations, reduces unnecessary overhead, and communicates that the method does not modify data.

---

# 📄 Cheat Sheet

| Feature | Purpose |
|---------|---------|
| `@Transactional` | Creates a transaction |
| Commit | Permanently saves changes |
| Rollback | Reverts all changes |
| `readOnly = true` | Optimises read operations |
| `rollbackFor` | Roll back for checked exceptions |
| `timeout` | Maximum execution time |
| `Propagation.REQUIRED` | Join existing or create a new transaction |
| `Propagation.REQUIRES_NEW` | Always create a new transaction |
| `Isolation.READ_COMMITTED` | Prevent dirty reads |

---

# 📝 Chapter Summary

In this chapter, you learned how Spring Boot manages database transactions using the `@Transactional` annotation. You explored the ACID properties, transaction lifecycle, commits, rollbacks, propagation behaviours, isolation levels, read-only transactions, and timeouts.

By applying transaction management correctly, you can ensure that business operations remain reliable, consistent, and fault-tolerant even when failures occur.

---

# 🚀 What's Next?

In **Chapter 12 – Pagination, Sorting, and Query Performance**, you'll learn how to efficiently retrieve large datasets using pagination, sort records dynamically, optimise database queries, avoid common performance issues such as the N+1 query problem, and build scalable applications capable of handling millions of records.
