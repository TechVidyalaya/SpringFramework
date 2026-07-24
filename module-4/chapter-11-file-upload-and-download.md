---
course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 11
title: File Upload and Download
difficulty: Intermediate
estimated_reading_time: 75 Minutes
estimated_coding_time: 60 Minutes
estimated_lab_time: 45 Minutes
version: 1.0
---

# Chapter 11
# File Upload and Download

> **"Modern applications don't just exchange data—they exchange documents, images, videos, and reports. File handling is a core feature of enterprise systems."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- Understand how file upload and download work over HTTP.
- Upload files using `MultipartFile`.
- Configure multipart support in Spring Boot.
- Store uploaded files safely.
- Download files using Spring MVC.
- Validate uploaded files.
- Follow enterprise best practices for secure file handling.

---

# Introduction

Imagine the Student Management System allows students to:

- Upload a profile picture
- Submit assignments
- Upload identity documents
- Download marksheets
- Download certificates

How can a Spring Boot application receive and serve files?

Unlike JSON data, files require a different approach.

Spring Boot simplifies file handling through **Multipart Requests** and the `MultipartFile` interface.

---

# Real-World Applications

File upload is used in almost every enterprise application.

Examples include:

- Resume uploads
- Profile photos
- Medical reports
- Tax documents
- Product images
- Invoice PDFs
- Assignment submissions
- Identity verification

Learning file handling is essential for full-stack development.

---

# How File Upload Works

A browser cannot send binary files as JSON.

Instead, it uses a special HTTP format called **multipart/form-data**.

```
Browser

↓

multipart/form-data

↓

Spring Boot

↓

MultipartFile

↓

Storage
```

Each uploaded file becomes a `MultipartFile` object inside the application.

---

# Multipart Requests

Example request:

```http
POST /students/profile-photo

Content-Type: multipart/form-data
```

The request contains:

- File data
- Form fields
- Headers
- Boundary information

Spring automatically parses the request into Java objects.

---

# What is MultipartFile?

`MultipartFile` represents an uploaded file.

It provides methods to:

- Read file content
- Get the filename
- Get file size
- Check whether the file is empty
- Save the file

Example:

```java
@PostMapping("/upload")
public String uploadFile(
        @RequestParam MultipartFile file) {

    return file.getOriginalFilename();

}
```

---

# Common MultipartFile Methods

| Method | Description |
|----------|-------------|
| `getOriginalFilename()` | Original file name |
| `getContentType()` | MIME type |
| `getSize()` | File size |
| `isEmpty()` | Checks whether the file is empty |
| `getBytes()` | Returns file contents |
| `getInputStream()` | Returns input stream |
| `transferTo()` | Saves the file |

---

# Creating a File Upload Endpoint

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @PostMapping("/profile-photo")
    public String uploadPhoto(
            @RequestParam MultipartFile file) {

        return "Uploaded: "
                + file.getOriginalFilename();

    }

}
```

Request:

```http
POST /students/profile-photo

Content-Type: multipart/form-data
```

---

# Upload Flow

```
Client

↓

Multipart Request

↓

DispatcherServlet

↓

Multipart Resolver

↓

MultipartFile

↓

Controller

↓

Storage
```

Spring automatically converts the uploaded file into a `MultipartFile`.

---

# Configuring Upload Size

Spring Boot allows upload limits to be configured.

Example:

```properties
spring.servlet.multipart.max-file-size=10MB

spring.servlet.multipart.max-request-size=20MB
```

This prevents excessively large uploads.

---

# Saving Files

The uploaded file can be stored on disk.

Example:

```java
@PostMapping("/upload")
public String upload(
        @RequestParam MultipartFile file)
        throws IOException {

    Path path = Paths.get(
            "uploads",
            file.getOriginalFilename());

    Files.copy(
            file.getInputStream(),
            path);

    return "File uploaded successfully.";

}
```

This saves the file inside the `uploads` directory.

---

# File Storage Options

Enterprise applications commonly store files in:

```
Local Storage

↓

Shared Network Storage

↓

Cloud Storage

↓

Object Storage
```

Examples:

- Amazon S3
- Azure Blob Storage
- Google Cloud Storage
- MinIO

For production systems, cloud object storage is generally preferred.

---

# Downloading Files

Spring Boot can also return files to clients.

Example:

```java
@GetMapping("/download")
public ResponseEntity<Resource> download() {

    Resource resource =
            new FileSystemResource(
                    "uploads/report.pdf");

    return ResponseEntity.ok()
            .body(resource);

}
```

The browser receives the file as the response body.

---

# Forcing File Download

To prompt the browser to download instead of displaying the file:

```java
return ResponseEntity.ok()

        .header(
                HttpHeaders.CONTENT_DISPOSITION,
                "attachment; filename=report.pdf")

        .body(resource);
```

This instructs the browser to download the file.

---

# Content Types

Every file has a MIME type.

Examples:

| File Type | Content-Type |
|------------|--------------|
| PDF | `application/pdf` |
| PNG | `image/png` |
| JPEG | `image/jpeg` |
| TXT | `text/plain` |
| ZIP | `application/zip` |

Returning the correct content type ensures clients handle files correctly.

---

# File Validation

Never trust uploaded files.

Validate:

- File size
- File type
- File extension
- Empty files

Example:

```java
if (file.isEmpty()) {

    throw new IllegalArgumentException(
            "File cannot be empty.");

}
```

---

# Restrict File Types

Example:

```java
String contentType =
        file.getContentType();

if (!"image/png".equals(contentType)
        && !"image/jpeg".equals(contentType)) {

    throw new IllegalArgumentException(
            "Only PNG and JPEG files are allowed.");

}
```

Always validate both MIME type and extension when appropriate.

---

# Security Considerations

Uploaded files may contain malicious content.

Potential risks include:

- Malware
- Viruses
- Executable files
- Script injection
- Path traversal attacks

Never trust:

- File names
- Extensions
- MIME types alone

---

# Safe File Naming

Instead of storing:

```
resume.pdf
```

Generate unique names.

Example:

```
3b5ef4b8-9f2d.pdf
```

This prevents filename collisions and reduces security risks.

---

# File Upload Architecture

```
Client

↓

Multipart Request

↓

DispatcherServlet

↓

MultipartFile

↓

Validation

↓

Storage Service

↓

Disk / Cloud

↓

Success Response
```

Notice that the controller delegates storage responsibilities to a dedicated service.

---

# Recommended Architecture

```
Controller

↓

FileStorageService

↓

Cloud Storage

↓

Database Metadata
```

The database stores metadata such as:

- File name
- File URL
- Upload date
- Owner
- Size

The actual file is stored separately.

---

# Common Mistakes

❌ Saving uploaded files directly inside the controller.

❌ Allowing unlimited upload sizes.

❌ Trusting the original filename.

❌ Accepting every file type.

❌ Storing uploaded files inside the project source directory.

---

# Best Practices

- Validate every uploaded file.
- Limit upload size.
- Generate unique filenames.
- Store files outside the application source code.
- Use cloud object storage for production.
- Scan files when handling sensitive uploads.
- Keep file handling logic inside a dedicated service.

---

# Industry Insight

Large-scale applications rarely store files inside the application server.

Typical enterprise architecture:

```
Frontend

↓

Spring Boot API

↓

File Storage Service

↓

Amazon S3

↓

Database (Metadata Only)
```

Benefits include:

- Scalability
- High availability
- Reduced server storage usage
- Easier backups
- Faster content delivery

---

# Interview Corner

## Basic

1. What is `MultipartFile`?
2. What is `multipart/form-data`?
3. Why can't files be sent as JSON?

---

## Intermediate

4. How do you configure maximum upload size in Spring Boot?
5. How do you return a downloadable file?
6. What is the purpose of the `Content-Disposition` header?

---

## Advanced

7. Why should uploaded files not be stored inside the project directory?
8. Explain a scalable architecture for file storage.
9. What security precautions should be taken during file uploads?

---

# Hands-on Lab

## Objective

Add profile photo upload functionality to the Student Management System.

### Tasks

1. Create an upload endpoint using `MultipartFile`.
2. Restrict uploads to PNG and JPEG files.
3. Limit upload size to 5 MB.
4. Generate a unique filename before saving.
5. Store the file inside an `uploads` directory.
6. Create a download endpoint.
7. Test uploads and downloads using Postman.

---

# Cheat Sheet

- Use `MultipartFile` for uploaded files.
- Upload requests use `multipart/form-data`.
- Configure upload limits in `application.properties`.
- Validate file type and size.
- Generate unique filenames.
- Use `ResponseEntity<Resource>` for downloads.
- Set the `Content-Disposition` header for downloadable files.
- Prefer cloud storage in production environments.

---

# Summary

File upload and download are essential features in modern web applications. Spring Boot simplifies file handling through `MultipartFile`, automatic multipart request processing, and flexible response handling. By validating uploads, generating safe filenames, separating storage logic into dedicated services, and leveraging cloud storage for production, developers can build secure, scalable, and maintainable file management solutions.

---

# What's Next?

➡ **Chapter 12 – Interceptors and Filters**

In the next chapter, you'll learn how Spring Boot intercepts requests before they reach controllers, how Filters and Interceptors differ, and how they are used for authentication, logging, request tracking, and cross-cutting concerns in enterprise applications.
