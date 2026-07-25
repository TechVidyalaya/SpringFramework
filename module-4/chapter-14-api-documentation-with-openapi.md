---

course: Industry Ready Java Developer
module: Module 4 - Spring MVC & RESTful Web Development
chapter: Chapter 14
title: API Documentation with OpenAPI
difficulty: Intermediate
estimated_reading_time: 85 Minutes
estimated_coding_time: 60 Minutes
estimated_lab_time: 45 Minutes
version: 1.0
------------

# Chapter 14

# API Documentation with OpenAPI

> **"An API is only useful when other developers can understand how to use it."**

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

* Understand why REST APIs require documentation.
* Differentiate between OpenAPI and Swagger.
* Generate API documentation automatically in Spring Boot.
* Explore and test endpoints using Swagger UI.
* Document controllers, operations, parameters, request bodies, and responses.
* Describe DTOs using OpenAPI annotations.
* Configure API information such as title, version, contact, and licence.
* Document validation errors and other API responses.
* Organise APIs using tags.
* Follow enterprise API documentation practices.

---

# Introduction

The Student Management System now provides several endpoints.

```text
GET    /api/v1/students
GET    /api/v1/students/{id}
POST   /api/v1/students
PUT    /api/v1/students/{id}
DELETE /api/v1/students/{id}
```

You understand these endpoints because you developed them.

But another developer may not know:

* What each endpoint does
* Which parameters are required
* What request body must be sent
* What response structure is returned
* Which validation rules apply
* Which HTTP status codes are possible
* What an error response looks like
* Whether authentication is required

You could explain everything through a document or email.

However, that documentation may quickly become outdated when the source code changes.

A better solution is to maintain an API description that tools and humans can both understand.

That is the purpose of the **OpenAPI Specification**.

---

# The Problem with Undocumented APIs

Suppose a frontend developer needs to create a student.

The developer discovers this endpoint:

```http
POST /api/v1/students
```

However, several questions remain.

```text
What fields should be sent?

Is the email mandatory?

What date format is expected?

Which department values are supported?

What happens when validation fails?

What status code is returned after creation?
```

Without documentation, the developer may repeatedly contact the backend team.

This creates:

* Development delays
* Incorrect integrations
* Repeated clarification meetings
* Invalid requests
* Inconsistent assumptions
* Increased testing effort

Good API documentation acts as a contract between API producers and API consumers.

---

# What Is API Documentation?

API documentation describes how clients can communicate with an API.

A useful API document normally explains:

* Available endpoints
* HTTP methods
* Request parameters
* Request headers
* Request body schemas
* Response body schemas
* HTTP status codes
* Validation requirements
* Authentication requirements
* Error responses
* Example requests and responses

API documentation should answer three questions:

```text
WHAT operation is available?

↓

HOW should the client call it?

↓

WHAT should the client expect in return?
```

---

# What Is OpenAPI?

The **OpenAPI Specification**, commonly abbreviated as **OAS**, is a standard format for describing HTTP APIs.

An OpenAPI description can be represented using:

* JSON
* YAML

A small OpenAPI document may look like this:

```yaml
openapi: 3.0.3

info:
  title: Student Management API
  version: 1.0.0

paths:
  /api/v1/students:
    get:
      summary: Retrieve all students
      responses:
        "200":
          description: Students retrieved successfully
```

This description can be understood by:

* Developers
* Documentation tools
* Testing tools
* Code generators
* API gateways
* Client SDK generators

---

# What Is Swagger?

The terms **OpenAPI** and **Swagger** are often used interchangeably, but they are not exactly the same.

## OpenAPI

OpenAPI is the specification used to describe an HTTP API.

It defines the structure of information such as:

* Paths
* Operations
* Parameters
* Schemas
* Responses
* Security requirements

## Swagger

Swagger refers to a collection of tools that work with OpenAPI descriptions.

Common Swagger tools include:

* Swagger UI
* Swagger Editor
* Swagger Codegen

The relationship can be understood as:

```text
OpenAPI

↓

The API description standard

Swagger UI

↓

A tool that displays the description
```

A useful comparison is:

```text
OpenAPI is the blueprint.

Swagger UI is a visual interface for the blueprint.
```

---

# OpenAPI and Swagger UI in Spring Boot

For Spring Boot applications, the `springdoc-openapi` library can inspect Spring MVC components and generate an OpenAPI description.

It analyses application elements such as:

* Controllers
* Request mappings
* Path variables
* Request parameters
* Request bodies
* Response types
* Validation annotations
* OpenAPI annotations

The generated description can then be displayed through Swagger UI.

```text
Spring MVC Controllers

↓

springdoc-openapi

↓

OpenAPI JSON

↓

Swagger UI

↓

Interactive API Documentation
```

---

# Adding the Dependency

For a Spring Boot MVC application, add the following Maven dependency:

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>${springdoc-openapi.version}</version>
</dependency>
```

Define the current compatible version in the Maven properties section:

```xml
<properties>
    <java.version>21</java.version>
    <springdoc-openapi.version>REPLACE_WITH_CURRENT_COMPATIBLE_VERSION</springdoc-openapi.version>
</properties>
```

Using a Maven property keeps dependency versions organised in one location.

For Gradle:

```groovy
implementation "org.springdoc:springdoc-openapi-starter-webmvc-ui:${springdocOpenApiVersion}"
```

After adding the dependency, restart the Spring Boot application.

No separate Swagger controller is required.

---

# Accessing the OpenAPI Description

By default, the machine-readable OpenAPI description is commonly available at:

```text
http://localhost:8080/v3/api-docs
```

The response is JSON.

A simplified response may look like this:

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "OpenAPI definition",
    "version": "v0"
  },
  "paths": {
    "/api/v1/students": {
      "get": {
        "responses": {
          "200": {
            "description": "OK"
          }
        }
      }
    }
  }
}
```

This endpoint is primarily intended for tools rather than end users.

---

# Accessing Swagger UI

Swagger UI provides a browser-based interface for exploring the API.

Depending on the library configuration, it is commonly available through:

```text
http://localhost:8080/swagger-ui/index.html
```

Swagger UI displays:

* API groups
* Endpoint paths
* HTTP methods
* Parameters
* Request schemas
* Response schemas
* Status codes
* Example values

It also allows developers to send test requests directly from the browser.

---

# Swagger UI Request Flow

```text
Developer

↓

Opens Swagger UI

↓

Swagger UI loads /v3/api-docs

↓

OpenAPI description is rendered

↓

Developer selects an endpoint

↓

Developer enters request data

↓

Swagger UI sends the HTTP request

↓

Spring Boot returns the response

↓

Swagger UI displays the result
```

Swagger UI is therefore both:

* API documentation
* An interactive API exploration tool

---

# Automatic Documentation

Consider the following controller:

```java
@RestController
@RequestMapping("/api/v1/students")
public class StudentController {

    @GetMapping
    public List<StudentResponse> findAll() {
        return studentService.findAll();
    }

    @GetMapping("/{id}")
    public StudentResponse findById(
            @PathVariable Long id) {

        return studentService.findById(id);
    }

    @PostMapping
    public ResponseEntity<StudentResponse> create(
            @Valid
            @RequestBody StudentRequest request) {

        StudentResponse response =
                studentService.create(request);

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(response);
    }
}
```

Without adding any OpenAPI annotations, `springdoc-openapi` can identify:

* `/api/v1/students`
* `/api/v1/students/{id}`
* GET and POST methods
* The `id` path variable
* The `StudentRequest` request body
* The `StudentResponse` response body

This gives the application a useful starting point.

However, automatic documentation cannot fully understand business meaning.

For professional documentation, explicit descriptions should be added.

---

# Important OpenAPI Annotations

Common OpenAPI annotations include:

| Annotation             | Purpose                         |
| ---------------------- | ------------------------------- |
| `@Tag`                 | Groups related endpoints        |
| `@Operation`           | Describes an API operation      |
| `@Parameter`           | Describes a parameter           |
| `@ApiResponse`         | Describes one response          |
| `@ApiResponses`        | Groups multiple responses       |
| `@RequestBody`         | Documents a request body        |
| `@Schema`              | Describes a model or field      |
| `@ExampleObject`       | Provides an example             |
| `@SecurityRequirement` | Documents security requirements |
| `@Hidden`              | Hides an endpoint or component  |

These annotations come from packages under:

```java
io.swagger.v3.oas.annotations
```

---

# Documenting a Controller with @Tag

The `@Tag` annotation groups related operations in Swagger UI.

```java
import io.swagger.v3.oas.annotations.tags.Tag;

@RestController
@RequestMapping("/api/v1/students")
@Tag(
    name = "Students",
    description = "Operations for managing students"
)
public class StudentController {

}
```

Swagger UI now displays these endpoints under the **Students** section.

Tags improve navigation when the application contains many controllers.

For example:

```text
Students

Courses

Teachers

Enrollments

Files
```

---

# Documenting an Operation

Use `@Operation` to explain what an endpoint does.

```java
import io.swagger.v3.oas.annotations.Operation;

@GetMapping("/{id}")
@Operation(
    summary = "Retrieve a student",
    description = """
        Retrieves a student using the unique student identifier.
        Returns 404 Not Found when the student does not exist.
        """
)
public StudentResponse findById(
        @PathVariable Long id) {

    return studentService.findById(id);
}
```

The `summary` should be short.

The `description` can provide additional details.

Good summary:

```text
Retrieve a student
```

Poor summary:

```text
This API is used to call the backend and get data
```

---

# Documenting Path Variables

Use `@Parameter` to explain a path variable.

```java
@GetMapping("/{id}")
@Operation(summary = "Retrieve a student")
public StudentResponse findById(

        @Parameter(
            description = "Unique identifier of the student",
            example = "101",
            required = true
        )
        @PathVariable Long id) {

    return studentService.findById(id);
}
```

Swagger UI displays:

* Parameter name
* Data type
* Description
* Required status
* Example value

---

# Documenting Request Parameters

Consider a paginated search endpoint:

```java
@GetMapping
public Page<StudentResponse> search(

        @RequestParam(defaultValue = "0")
        int page,

        @RequestParam(defaultValue = "20")
        int size,

        @RequestParam(required = false)
        String department) {

    return studentService.search(
            page,
            size,
            department);
}
```

Add meaningful parameter documentation:

```java
@GetMapping
@Operation(
    summary = "Search students",
    description = "Returns a paginated list of students."
)
public Page<StudentResponse> search(

        @Parameter(
            description = "Zero-based page number",
            example = "0"
        )
        @RequestParam(defaultValue = "0")
        int page,

        @Parameter(
            description = "Number of records per page",
            example = "20"
        )
        @RequestParam(defaultValue = "20")
        int size,

        @Parameter(
            description = "Optional department filter",
            example = "Computer Science"
        )
        @RequestParam(required = false)
        String department) {

    return studentService.search(
            page,
            size,
            department);
}
```

---

# Documenting Request DTOs

Consider the following request DTO:

```java
public class StudentRequest {

    @NotBlank
    private String name;

    @NotBlank
    @Email
    private String email;

    @NotBlank
    private String department;

    @Past
    private LocalDate dateOfBirth;
}
```

Swagger UI can infer the field names and types.

However, `@Schema` makes the API contract clearer.

```java
import io.swagger.v3.oas.annotations.media.Schema;

@Schema(
    name = "StudentRequest",
    description = "Data required to create or update a student"
)
public class StudentRequest {

    @Schema(
        description = "Full name of the student",
        example = "Ananya Das",
        minLength = 2,
        maxLength = 100
    )
    @NotBlank(message = "Student name is required.")
    @Size(min = 2, max = 100)
    private String name;

    @Schema(
        description = "Unique email address of the student",
        example = "ananya.das@example.com"
    )
    @NotBlank(message = "Email is required.")
    @Email(message = "Email must be valid.")
    private String email;

    @Schema(
        description = "Department in which the student is enrolled",
        example = "Computer Science"
    )
    @NotBlank(message = "Department is required.")
    private String department;

    @Schema(
        description = "Student's date of birth in ISO format",
        example = "2003-08-14",
        type = "string",
        format = "date"
    )
    @Past(message = "Date of birth must be in the past.")
    private LocalDate dateOfBirth;

    // Getters and setters
}
```

The generated documentation now communicates:

* Field meaning
* Example value
* Expected format
* Length constraints
* Validation requirements

---

# Documenting Response DTOs

Response models should also be documented.

```java
@Schema(
    name = "StudentResponse",
    description = "Student information returned by the API"
)
public class StudentResponse {

    @Schema(
        description = "Unique identifier of the student",
        example = "101"
    )
    private Long id;

    @Schema(
        description = "Full name of the student",
        example = "Ananya Das"
    )
    private String name;

    @Schema(
        description = "Email address of the student",
        example = "ananya.das@example.com"
    )
    private String email;

    @Schema(
        description = "Student's department",
        example = "Computer Science"
    )
    private String department;

    @Schema(
        description = "Date and time when the student was created",
        example = "2026-07-25T10:30:00"
    )
    private LocalDateTime createdAt;

    // Getters and setters
}
```

---

# Hiding Internal Fields

Not every field should appear in API documentation.

For example, an internal field may not be part of the public contract.

```java
@Schema(hidden = true)
private String internalReference;
```

However, the better architectural approach is usually to avoid placing internal fields in public DTOs.

```text
Database Entity

↓

Internal fields

StudentResponse DTO

↓

Public API fields only
```

OpenAPI annotations should support a clean DTO design, not compensate for poor separation.

---

# Documenting HTTP Responses

An endpoint may return multiple outcomes.

For example:

```text
200 OK

400 Bad Request

404 Not Found

500 Internal Server Error
```

Use `@ApiResponse` to document each outcome.

```java
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;

@GetMapping("/{id}")
@Operation(summary = "Retrieve a student")
@ApiResponses({

    @ApiResponse(
        responseCode = "200",
        description = "Student retrieved successfully"
    ),

    @ApiResponse(
        responseCode = "404",
        description = "Student was not found"
    ),

    @ApiResponse(
        responseCode = "500",
        description = "Unexpected server error"
    )
})
public StudentResponse findById(
        @PathVariable Long id) {

    return studentService.findById(id);
}
```

This documents the possible status codes, but it does not yet describe their response bodies.

---

# Documenting Response Schemas

Suppose successful responses use `StudentResponse`, while errors use `ApiErrorResponse`.

```java
@Schema(
    name = "ApiErrorResponse",
    description = "Standard API error response"
)
public class ApiErrorResponse {

    @Schema(
        example = "2026-07-25T10:45:30"
    )
    private LocalDateTime timestamp;

    @Schema(
        example = "404"
    )
    private int status;

    @Schema(
        example = "Not Found"
    )
    private String error;

    @Schema(
        example = "Student not found with id: 101"
    )
    private String message;

    @Schema(
        example = "/api/v1/students/101"
    )
    private String path;

    // Getters and setters
}
```

The controller can reference these schemas.

```java
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;

@GetMapping("/{id}")
@Operation(
    summary = "Retrieve a student",
    description = "Returns a student using the supplied identifier."
)
@ApiResponses({

    @ApiResponse(
        responseCode = "200",
        description = "Student retrieved successfully",
        content = @Content(
            mediaType = "application/json",
            schema = @Schema(
                implementation = StudentResponse.class
            )
        )
    ),

    @ApiResponse(
        responseCode = "404",
        description = "Student was not found",
        content = @Content(
            mediaType = "application/json",
            schema = @Schema(
                implementation = ApiErrorResponse.class
            )
        )
    )
})
public StudentResponse findById(
        @Parameter(
            description = "Unique student identifier",
            example = "101"
        )
        @PathVariable Long id) {

    return studentService.findById(id);
}
```

---

# Documenting a Create Operation

Creating a resource commonly returns:

```http
201 Created
```

A documented create operation may look like this:

```java
@PostMapping
@Operation(
    summary = "Create a student",
    description = """
        Creates a new student after validating the supplied data.
        The student's email address must be unique.
        """
)
@ApiResponses({

    @ApiResponse(
        responseCode = "201",
        description = "Student created successfully",
        content = @Content(
            mediaType = "application/json",
            schema = @Schema(
                implementation = StudentResponse.class
            )
        )
    ),

    @ApiResponse(
        responseCode = "400",
        description = "Request validation failed",
        content = @Content(
            mediaType = "application/json",
            schema = @Schema(
                implementation = ValidationErrorResponse.class
            )
        )
    ),

    @ApiResponse(
        responseCode = "409",
        description = "A student with the email already exists",
        content = @Content(
            mediaType = "application/json",
            schema = @Schema(
                implementation = ApiErrorResponse.class
            )
        )
    )
})
public ResponseEntity<StudentResponse> create(

        @Valid
        @org.springframework.web.bind.annotation.RequestBody
        StudentRequest request) {

    StudentResponse response =
            studentService.create(request);

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(response);
}
```

---

# Avoiding @RequestBody Import Confusion

Spring and OpenAPI both provide an annotation named `RequestBody`.

Spring annotation:

```java
org.springframework.web.bind.annotation.RequestBody
```

OpenAPI annotation:

```java
io.swagger.v3.oas.annotations.parameters.RequestBody
```

The Spring annotation controls runtime request binding.

The OpenAPI annotation adds documentation metadata.

To avoid confusing imports, many developers:

* Import Spring's `@RequestBody`
* Use the fully qualified OpenAPI annotation only when needed

Example:

```java
@PostMapping
public StudentResponse create(

        @io.swagger.v3.oas.annotations.parameters.RequestBody(
            description = "Student information",
            required = true
        )
        @Valid
        @RequestBody StudentRequest request) {

    return studentService.create(request);
}
```

---

# Documenting Validation Errors

Suppose validation failures return this structure:

```json
{
  "timestamp": "2026-07-25T11:30:00",
  "status": 400,
  "error": "Validation Failed",
  "message": "One or more fields are invalid.",
  "path": "/api/v1/students",
  "fieldErrors": {
    "name": "Student name is required.",
    "email": "Email must be valid."
  }
}
```

Create a dedicated DTO:

```java
@Schema(
    name = "ValidationErrorResponse",
    description = "Response returned when request validation fails"
)
public class ValidationErrorResponse {

    @Schema(
        example = "2026-07-25T11:30:00"
    )
    private LocalDateTime timestamp;

    @Schema(example = "400")
    private int status;

    @Schema(example = "Validation Failed")
    private String error;

    @Schema(
        example = "One or more fields are invalid."
    )
    private String message;

    @Schema(
        example = "/api/v1/students"
    )
    private String path;

    @Schema(
        description = "Validation messages grouped by field",
        example = """
            {
              "name": "Student name is required.",
              "email": "Email must be valid."
            }
            """
    )
    private Map<String, String> fieldErrors;

    // Getters and setters
}
```

Documenting errors is as important as documenting successful responses.

Frontend developers need to know how errors should be parsed and displayed.

---

# Adding Example Responses

Examples make documentation easier to understand.

```java
@ApiResponse(
    responseCode = "404",
    description = "Student was not found",
    content = @Content(
        mediaType = "application/json",
        schema = @Schema(
            implementation = ApiErrorResponse.class
        ),
        examples = @ExampleObject(
            name = "Student not found",
            value = """
                {
                  "timestamp": "2026-07-25T10:45:30",
                  "status": 404,
                  "error": "Not Found",
                  "message": "Student not found with id: 101",
                  "path": "/api/v1/students/101"
                }
                """
        )
    )
)
```

Examples are especially useful when:

* Fields contain special formats
* Responses are nested
* Multiple error scenarios exist
* A third-party developer consumes the API

---

# Configuring API Information

The automatically generated API title and version are usually too generic.

Create an OpenAPI configuration class.

```java
package com.techvidyalaya.student.config;

import io.swagger.v3.oas.models.Contact;
import io.swagger.v3.oas.models.Info;
import io.swagger.v3.oas.models.License;
import io.swagger.v3.oas.models.OpenAPI;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI studentManagementOpenAPI() {

        Contact contact = new Contact()
                .name("TechVidyalaya API Team")
                .email("api-support@example.com");

        License license = new License()
                .name("Proprietary");

        Info info = new Info()
                .title("Student Management API")
                .description("""
                    REST API for managing students, courses,
                    teachers, and enrolments.
                    """)
                .version("1.0.0")
                .contact(contact)
                .license(license);

        return new OpenAPI()
                .info(info);
    }
}
```

Swagger UI now displays:

* API title
* Description
* Version
* Contact details
* Licence information

Use real organisational contact information in production documentation.

---

# Adding Server Information

An API may run in multiple environments.

For example:

```text
Development

Testing

Production
```

These servers can be documented.

```java
import io.swagger.v3.oas.models.servers.Server;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI studentManagementOpenAPI() {

        Server localServer = new Server()
                .url("http://localhost:8080")
                .description("Local development server");

        Server productionServer = new Server()
                .url("https://api.example.com")
                .description("Production server");

        Info info = new Info()
                .title("Student Management API")
                .version("1.0.0");

        return new OpenAPI()
                .info(info)
                .addServersItem(localServer)
                .addServersItem(productionServer);
    }
}
```

Avoid exposing private internal server addresses in public documentation.

---

# Configuring Documentation Paths

The OpenAPI description and Swagger UI paths can be customised.

```properties
springdoc.api-docs.path=/api-docs

springdoc.swagger-ui.path=/swagger-ui.html
```

After configuration, the documentation may be available through:

```text
http://localhost:8080/api-docs

http://localhost:8080/swagger-ui.html
```

Custom paths can make documentation URLs easier to standardise across services.

---

# Sorting Operations and Tags

For larger APIs, sorting improves usability.

```properties
springdoc.swagger-ui.operations-sorter=method

springdoc.swagger-ui.tags-sorter=alpha
```

This can arrange:

* Operations by HTTP method
* Tags alphabetically

Documentation should remain easy to navigate as the API grows.

---

# Disabling Documentation in Production

Swagger UI can expose valuable information about an application's API surface.

Some organisations permit internal production documentation.

Others disable it or restrict it through authentication and network controls.

Documentation can be disabled using environment-specific configuration.

Development profile:

```properties
springdoc.api-docs.enabled=true

springdoc.swagger-ui.enabled=true
```

Production profile:

```properties
springdoc.api-docs.enabled=false

springdoc.swagger-ui.enabled=false
```

Alternatively, documentation endpoints can be protected using Spring Security.

The correct decision depends on the organisation's security and integration requirements.

---

# Documenting File Uploads

The Student Management System includes a profile-photo upload endpoint.

```java
@PostMapping(
    value = "/{id}/profile-photo",
    consumes = MediaType.MULTIPART_FORM_DATA_VALUE
)
@Operation(
    summary = "Upload a profile photo",
    description = """
        Uploads a PNG or JPEG profile photo for a student.
        The maximum supported file size is 5 MB.
        """
)
@ApiResponses({

    @ApiResponse(
        responseCode = "200",
        description = "Profile photo uploaded successfully"
    ),

    @ApiResponse(
        responseCode = "400",
        description = "The file is empty, too large, or unsupported"
    ),

    @ApiResponse(
        responseCode = "404",
        description = "Student was not found"
    )
})
public FileResponse uploadProfilePhoto(

        @Parameter(
            description = "Unique student identifier",
            example = "101"
        )
        @PathVariable Long id,

        @Parameter(
            description = "PNG or JPEG profile image",
            required = true
        )
        @RequestPart("file")
        MultipartFile file) {

    return fileStorageService.uploadProfilePhoto(
            id,
            file);
}
```

The `consumes` attribute makes the multipart contract clear.

---

# Documenting File Downloads

A file download endpoint returns binary content.

```java
@GetMapping(
    value = "/{id}/profile-photo",
    produces = {
        MediaType.IMAGE_JPEG_VALUE,
        MediaType.IMAGE_PNG_VALUE
    }
)
@Operation(
    summary = "Download a profile photo"
)
@ApiResponses({

    @ApiResponse(
        responseCode = "200",
        description = "Profile photo returned successfully",
        content = @Content(
            mediaType = "application/octet-stream",
            schema = @Schema(
                type = "string",
                format = "binary"
            )
        )
    ),

    @ApiResponse(
        responseCode = "404",
        description = "Student or profile photo was not found"
    )
})
public ResponseEntity<Resource> downloadProfilePhoto(
        @PathVariable Long id) {

    return fileStorageService
            .downloadProfilePhoto(id);
}
```

The binary schema communicates that the response contains a file rather than JSON.

---

# Complete StudentController Documentation

The following example brings the major concepts together.

```java
package com.techvidyalaya.student.controller;

import com.techvidyalaya.student.dto.ApiErrorResponse;
import com.techvidyalaya.student.dto.StudentRequest;
import com.techvidyalaya.student.dto.StudentResponse;
import com.techvidyalaya.student.dto.ValidationErrorResponse;
import com.techvidyalaya.student.service.StudentService;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import io.swagger.v3.oas.annotations.tags.Tag;
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/v1/students")
@Tag(
    name = "Students",
    description = "Create, retrieve, update, and delete students"
)
public class StudentController {

    private final StudentService studentService;

    public StudentController(
            StudentService studentService) {

        this.studentService = studentService;
    }

    @GetMapping
    @Operation(
        summary = "Retrieve all students",
        description = "Returns all students currently available."
    )
    @ApiResponse(
        responseCode = "200",
        description = "Students retrieved successfully"
    )
    public List<StudentResponse> findAll() {

        return studentService.findAll();
    }

    @GetMapping("/{id}")
    @Operation(
        summary = "Retrieve a student",
        description = "Returns a student using the unique identifier."
    )
    @ApiResponses({

        @ApiResponse(
            responseCode = "200",
            description = "Student retrieved successfully",
            content = @Content(
                schema = @Schema(
                    implementation =
                            StudentResponse.class
                )
            )
        ),

        @ApiResponse(
            responseCode = "404",
            description = "Student was not found",
            content = @Content(
                schema = @Schema(
                    implementation =
                            ApiErrorResponse.class
                )
            )
        )
    })
    public StudentResponse findById(

            @Parameter(
                description = "Unique student identifier",
                example = "101",
                required = true
            )
            @PathVariable Long id) {

        return studentService.findById(id);
    }

    @PostMapping
    @Operation(
        summary = "Create a student",
        description = "Creates a student using validated request data."
    )
    @ApiResponses({

        @ApiResponse(
            responseCode = "201",
            description = "Student created successfully",
            content = @Content(
                schema = @Schema(
                    implementation =
                            StudentResponse.class
                )
            )
        ),

        @ApiResponse(
            responseCode = "400",
            description = "Request validation failed",
            content = @Content(
                schema = @Schema(
                    implementation =
                            ValidationErrorResponse.class
                )
            )
        ),

        @ApiResponse(
            responseCode = "409",
            description = "Student email already exists",
            content = @Content(
                schema = @Schema(
                    implementation =
                            ApiErrorResponse.class
                )
            )
        )
    })
    public ResponseEntity<StudentResponse> create(

            @Valid
            @RequestBody StudentRequest request) {

        StudentResponse response =
                studentService.create(request);

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(response);
    }

    @PutMapping("/{id}")
    @Operation(
        summary = "Update a student",
        description = "Replaces the editable details of a student."
    )
    @ApiResponses({

        @ApiResponse(
            responseCode = "200",
            description = "Student updated successfully"
        ),

        @ApiResponse(
            responseCode = "400",
            description = "Request validation failed"
        ),

        @ApiResponse(
            responseCode = "404",
            description = "Student was not found"
        )
    })
    public StudentResponse update(

            @Parameter(
                description = "Unique student identifier",
                example = "101"
            )
            @PathVariable Long id,

            @Valid
            @RequestBody StudentRequest request) {

        return studentService.update(
                id,
                request);
    }

    @DeleteMapping("/{id}")
    @Operation(
        summary = "Delete a student",
        description = "Permanently removes a student."
    )
    @ApiResponses({

        @ApiResponse(
            responseCode = "204",
            description = "Student deleted successfully"
        ),

        @ApiResponse(
            responseCode = "404",
            description = "Student was not found"
        )
    })
    public ResponseEntity<Void> delete(

            @Parameter(
                description = "Unique student identifier",
                example = "101"
            )
            @PathVariable Long id) {

        studentService.delete(id);

        return ResponseEntity.noContent().build();
    }
}
```

---

# Annotation-Based vs Design-First Documentation

There are two common approaches to creating OpenAPI descriptions.

## Code-First Approach

The application code is written first.

The OpenAPI description is generated from:

* Controllers
* DTOs
* Validation annotations
* OpenAPI annotations

```text
Java Code

↓

Generated OpenAPI Description

↓

Documentation
```

Advantages:

* Convenient for Spring Boot teams
* Documentation stays close to code
* Easy to begin
* Less duplicated work

Risks:

* Developers may forget descriptions
* Documentation may reflect implementation rather than API design
* Generated contracts may be inconsistent without standards

---

## Design-First Approach

The API contract is designed before implementation.

```text
OpenAPI Contract

↓

Review and Approval

↓

Server Implementation

↓

Client Generation
```

Advantages:

* Encourages contract-first thinking
* Frontend and backend teams can work in parallel
* Suitable for public and partner APIs
* Supports governance and early review

Risks:

* Requires disciplined contract maintenance
* Implementation and contract can drift
* Adds an additional design workflow

---

# Hybrid Approach

Many enterprise teams use a hybrid approach.

```text
API requirements

↓

Initial OpenAPI design

↓

Team review

↓

Spring Boot implementation

↓

Automated contract verification
```

The important goal is not choosing a fashionable approach.

The goal is ensuring that:

```text
Documentation

=

Actual API behaviour
```

---

# OpenAPI as an API Contract

OpenAPI is more than a documentation format.

It can support:

* Client SDK generation
* Server stub generation
* Mock servers
* Contract testing
* API gateway configuration
* Security review
* Automated testing
* Developer portals
* API governance

This makes accurate documentation part of software engineering rather than an optional writing task.

---

# Documentation and API Versioning

The documentation version should clearly communicate the API contract version.

Example endpoint version:

```text
/api/v1/students
```

Example API information:

```java
new Info()
    .title("Student Management API")
    .version("1.0.0");
```

These values serve related but different purposes.

* `/v1` represents the major API route version.
* `1.0.0` can represent the documentation or contract release.

Avoid changing API behaviour without updating the documentation.

---

# Documenting Deprecated Endpoints

When replacing an endpoint, mark the old operation as deprecated.

```java
@GetMapping("/all")
@Operation(
    summary = "Retrieve all students using the legacy endpoint",
    deprecated = true
)
public List<StudentResponse> findAllLegacy() {

    return studentService.findAll();
}
```

Deprecation communicates:

```text
The endpoint still works.

↓

Clients should stop using it.

↓

A replacement should be adopted.
```

Do not remove a public API immediately without a migration strategy.

---

# Hiding Internal Endpoints

Some endpoints should not appear in public documentation.

Use `@Hidden` carefully.

```java
import io.swagger.v3.oas.annotations.Hidden;

@Hidden
@GetMapping("/internal/cache")
public CacheStatus inspectCache() {

    return cacheService.inspect();
}
```

Hiding an endpoint from Swagger UI does not secure it.

```text
Hidden documentation

≠

Protected endpoint
```

Actual access control must be implemented using Spring Security or another security layer.

---

# Keeping Documentation Accurate

Generated documentation can still be inaccurate when annotations do not match runtime behaviour.

For example:

```java
@ApiResponse(
    responseCode = "201",
    description = "Student created"
)
```

But the controller actually returns:

```java
return ResponseEntity.ok(response);
```

The documentation says `201`.

The application returns `200`.

This creates a contract mismatch.

Documentation should be tested and reviewed like source code.

---

# Common Mistakes

## Mistake 1: Treating Swagger UI as the specification

Swagger UI is only a visual tool.

The OpenAPI description is the actual machine-readable API contract.

---

## Mistake 2: Writing vague operation descriptions

Poor:

```text
Gets data
```

Better:

```text
Retrieves a student using the unique student identifier
```

---

## Mistake 3: Documenting only successful responses

Clients must also understand:

* Validation failures
* Missing resources
* Conflicts
* Authentication failures
* Server errors

---

## Mistake 4: Exposing entities directly

Document request and response DTOs rather than persistence entities.

```text
API Contract DTO

≠

Database Entity
```

---

## Mistake 5: Adding annotations without maintaining them

Incorrect documentation is often worse than missing documentation because clients trust the published contract.

---

## Mistake 6: Exposing Swagger UI publicly without review

Documentation endpoints should be:

* Disabled
* Restricted
* Authenticated
* Or intentionally published

The choice must be deliberate.

---

## Mistake 7: Assuming hidden means secure

`@Hidden` only removes an operation from generated documentation.

It does not prevent HTTP access.

---

## Mistake 8: Filling controllers with excessive documentation

Annotations can make controller methods difficult to read.

For large systems, consider:

* Reusable response annotations
* Shared schema classes
* Interface-based API contracts
* Centralised error documentation
* Design-first OpenAPI files

Documentation should improve clarity rather than bury the implementation.

---

# Best Practices

* Treat API documentation as part of the product.
* Document request and response DTOs.
* Use clear operation summaries.
* Explain business meaning, not only Java types.
* Document successful and unsuccessful responses.
* Include realistic examples.
* Keep status codes aligned with runtime behaviour.
* Use tags to group related endpoints.
* Protect or disable documentation where required.
* Avoid exposing internal implementation details.
* Review documentation during code review.
* Version public API contracts.
* Automate checks where possible.

---

# Industry Insight

In enterprise development, API documentation supports several teams.

```text
Backend Developers

Frontend Developers

Mobile Developers

Quality Engineers

Automation Engineers

Partner Teams

Support Teams

Security Teams

Architecture Teams
```

A poorly documented API increases communication overhead for every team.

A clearly documented API allows teams to integrate independently and reduces dependency on individual developers.

The real value of OpenAPI is not the attractive Swagger UI page.

The real value is creating a dependable, machine-readable contract.

---

# Interview Corner

## Basic Questions

1. What is API documentation?
2. What is the OpenAPI Specification?
3. What is Swagger UI?
4. What is the difference between OpenAPI and Swagger?
5. What is the purpose of `@Operation`?
6. What is the purpose of `@Schema`?
7. What does `@Tag` do?

---

## Intermediate Questions

8. How does `springdoc-openapi` generate documentation?
9. How do you document multiple HTTP responses?
10. How do you document path variables?
11. How do you document request and response DTOs?
12. How do you customise API title and version?
13. How do you document a multipart file upload?
14. How can Swagger UI be disabled in production?
15. Why should error responses be documented?

---

## Advanced Questions

16. Explain code-first and design-first API documentation.
17. How can OpenAPI support contract testing?
18. Why is generated API documentation sometimes inaccurate?
19. How would you prevent API documentation from drifting from the implementation?
20. How would you document security requirements?
21. Why does hiding an endpoint not secure it?
22. How would you manage OpenAPI documentation across multiple microservices?
23. How can an OpenAPI contract help frontend and backend teams work in parallel?

---

# Hands-on Lab

## Objective

Add interactive OpenAPI documentation to the Student Management System.

---

## Part 1: Add OpenAPI Support

Add the `springdoc-openapi` Swagger UI starter to the project.

Start the application and verify:

```text
/v3/api-docs

/swagger-ui/index.html
```

---

## Part 2: Configure API Information

Create `OpenApiConfig`.

Add:

* API title
* Description
* Version
* Contact information
* Licence information
* Local server information

---

## Part 3: Document StudentController

Add:

* `@Tag`
* `@Operation`
* `@Parameter`
* `@ApiResponse`
* `@ApiResponses`

Document all student CRUD endpoints.

---

## Part 4: Document DTOs

Add `@Schema` documentation to:

```text
StudentRequest

StudentResponse

ApiErrorResponse

ValidationErrorResponse
```

Include descriptions and realistic examples.

---

## Part 5: Document Failure Scenarios

Document:

```text
400 Bad Request

404 Not Found

409 Conflict

500 Internal Server Error
```

Make sure the documented response DTOs match the responses returned by the global exception handler.

---

## Part 6: Test Through Swagger UI

Using Swagger UI:

1. Retrieve all students.
2. Retrieve an existing student.
3. Retrieve a missing student.
4. Create a valid student.
5. Submit an invalid student.
6. Update a student.
7. Delete a student.
8. Verify the returned status codes and response bodies.

---

## Part 7: Compare Documentation with Behaviour

Check each endpoint.

| Check               | Expected Result             |
| ------------------- | --------------------------- |
| HTTP method         | Matches controller mapping  |
| Path                | Matches actual endpoint     |
| Required parameters | Correctly identified        |
| Request schema      | Matches DTO                 |
| Validation rules    | Visible and accurate        |
| Success status      | Matches controller response |
| Error statuses      | Match exception handling    |
| Examples            | Valid and realistic         |

Fix every mismatch you identify.

---

# Lab Challenge

Add documentation for the following endpoint:

```http
POST /api/v1/students/{id}/profile-photo
```

Requirements:

* Accept `multipart/form-data`
* Accept PNG and JPEG files
* Maximum size: 5 MB
* Return `200 OK` after successful upload
* Return `400 Bad Request` for invalid files
* Return `404 Not Found` when the student does not exist

Document:

* Path variable
* File parameter
* Consumed media type
* Success response
* Error responses

---

# Project Structure

After completing this chapter, the Student Management System may contain:

```text
src/main/java
└── com.techvidyalaya.student
    ├── config
    │   ├── OpenApiConfig.java
    │   └── WebConfig.java
    │
    ├── controller
    │   └── StudentController.java
    │
    ├── dto
    │   ├── StudentRequest.java
    │   ├── StudentResponse.java
    │   ├── ApiErrorResponse.java
    │   └── ValidationErrorResponse.java
    │
    ├── exception
    │   ├── StudentNotFoundException.java
    │   └── GlobalExceptionHandler.java
    │
    ├── service
    │   └── StudentService.java
    │
    └── StudentManagementApplication.java
```

---

# Cheat Sheet

## Dependency

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>${springdoc-openapi.version}</version>
</dependency>
```

---

## OpenAPI Description

```text
/v3/api-docs
```

---

## Swagger UI

```text
/swagger-ui/index.html
```

---

## Controller Group

```java
@Tag(
    name = "Students",
    description = "Student management operations"
)
```

---

## Operation Description

```java
@Operation(
    summary = "Retrieve a student",
    description = "Returns a student using the identifier."
)
```

---

## Parameter Description

```java
@Parameter(
    description = "Unique student identifier",
    example = "101"
)
```

---

## Response Description

```java
@ApiResponse(
    responseCode = "200",
    description = "Student retrieved successfully"
)
```

---

## DTO Description

```java
@Schema(
    description = "Full name of the student",
    example = "Ananya Das"
)
```

---

## Disable Swagger UI

```properties
springdoc.swagger-ui.enabled=false
```

---

## Disable OpenAPI Description

```properties
springdoc.api-docs.enabled=false
```

---

# Summary

The OpenAPI Specification provides a standard, machine-readable way to describe HTTP APIs. Swagger UI uses that description to create interactive documentation that developers can explore and test.

In Spring Boot, `springdoc-openapi` can generate an initial API description by inspecting Spring MVC controllers, mappings, parameters, DTOs, and validation rules. OpenAPI annotations such as `@Tag`, `@Operation`, `@Parameter`, `@ApiResponse`, and `@Schema` enrich the generated description with business meaning and examples.

Professional API documentation must describe more than successful requests. It should include validation failures, missing resources, conflicts, security requirements, supported media types, and standard error formats.

Most importantly, documentation must match actual application behaviour. OpenAPI should be treated as an API contract that is reviewed, tested, versioned, and maintained alongside the source code.

---

# What's Next?

➡ **Chapter 15 – Building a Complete REST API**

In the next chapter, you will combine everything learned throughout Module 4 to build a structured Student Management REST API.

The final project will include:

* RESTful endpoint design
* Request and response DTOs
* Bean Validation
* Global exception handling
* File upload and download
* Filters and Interceptors
* CORS configuration
* OpenAPI documentation
* Consistent HTTP status codes
* A clean layered architecture

This project will become the foundation for adding database persistence, security, testing, microservices, containerisation, messaging, cloud deployment, and AI capabilities in the upcoming modules.

