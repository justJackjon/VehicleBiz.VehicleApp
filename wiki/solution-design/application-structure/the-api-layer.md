# The API Layer

## Project Overview: VehicleBiz.VehicleApp.Api

### Introduction

In the `VehicleBiz.VehicleApp.Api` project, the focus is on providing a clear interface for data exchange, rigorous error handling, and efficient management of requests and responses. Although the nuances of Domain-Driven Design (DDD) predominantly reside within the domain layer, certain principles can still be effectively adopted at the API layer.

### Core Concepts

#### **1. Application Services**

Though the domain encapsulates core business logic, the API layer employs application services to coordinate operations and interface with the domain layer. These services act as a bridge, handling tasks that don't fit within the domain's purview but are crucial for the API's functionality.

#### **2. DTOs (Data Transfer Objects)**

DTOs serve to protect domain entities from direct external exposure. They define the structure of data exchanged between the API and its consumers, ensuring a consistent and controlled data flow which limits any exposure to internal implementation details.

#### 3. Global **Exception Handling**

A centralised approach to error handling ensures that errors are consistently managed, providing clear and descriptive feedback.

#### **4. Request Validation**

Validation is vital to ensuring data integrity and safeguarding the domain. The API layer is responsible for handling request-level validations, including:

* Ensuring mandatory fields like VIN, registration mark, and valuation are all present.
* Adherence to specific formats, such as the registration mark following the format of 2 uppercase letters, 2 numbers and 3 uppercase letters.
* Ensuring unique constraints, like the restriction that a vehicle cannot have a VIN or registration mark that already exists in the database.

### Proposed Directory Structure

```bash
VehicleBiz.VehicleApp.Api/
|
|-- Features/
|   |
|   |-- v1/
|   |   |
|   |   |-- SearchVehicles/
|   |   |   |-- SearchVehiclesFunction.cs
|   |   |   |-- VehicleSearchDTO.cs
|   |   |   |-- VehicleSearchValidator.cs
|   |   |   |-- VehicleSearchMapperProfile.cs  # (if using AutoMapper)
|   |   |
|   |   |-- GetVehicle/
|   |   |   |-- GetVehicleFunction.cs
|   |   |   |-- VehicleDTO.cs
|   |   |   |-- VehicleMapperProfile.cs
|   |   |
|   |   |-- AddVehicle/
|   |   |   |-- AddVehicleFunction.cs
|   |   |   |-- AddVehicleRequest.cs
|   |   |   |-- AddVehicleValidator.cs
|   |   |   |-- AddVehicleMapperProfile.cs
|   |   |
|   |   |-- UpdateVehicle/
|   |   |   |-- UpdateVehicleFunction.cs
|   |   |   |-- UpdateVehicleRequest.cs
|   |   |   |-- UpdateVehicleValidator.cs
|   |   |   |-- UpdateVehicleMapperProfile.cs
|   |   |
|   |   |-- DeleteVehicle/
|   |       |-- DeleteVehicleFunction.cs
|   |
|   |-- v2/
|   |   |-- ...  # (features specific to v2)
|   |
|   |-- ... # (other versions as they evolve)
|
|-- Middleware/
|   |
|   |-- ExceptionMiddleware.cs
|
|-- Filters/
    |
    |-- AuditingRequestFilterAttribute.cs

# NOTES:
# > Features/
#   The Features directory contains version-specific subdirectories, with each
#   nested subdirectory representing a distinct feature or capability.
#
# > Function Files:
#   Each specific Azure Function is kept within it's respective feature directory,
#   and nested within a versioned parent directory. This directory structure allows
#   for swift identification of the function and its affiliated resources.
#
# > DTOs, Validators, and Mappers:
#   Instead of a general DTOs or Validators directory, each feature directory will
#   contain its own DTOs, validators, mappers, etc., specifically for that feature.
#   This makes it clear what each part of the system does and where to find or place
#   code related to a specific operation.
#
# > Middleware:
#   This remains at the root as middleware is generally a cross-cutting concern and
#   applied across multiple features.
#
# > Filters:
#   Filters, like middleware, address cross-cutting concerns but are specifically
#   designed for pre or post processing of function executions. A
#   `AuditingRequestFilterAttribute`, for instance, could be used to log or monitor
#   requests for audit purposes.
```

### Proposed Implementation

Precise implementation detail is outside the scope of this document, however, in alignment with the [user stories](../../functional-requirements/user-stories.md), the following functions are suggested to manage CRUD operations for the vehicle data within the system:

* **SearchVehiclesFunction**:
  * **Trigger**: HTTP (GET)
  * **Purpose**: Enables users to search for vehicles based on multiple criteria.
  * **Implementation**: Uses indexed properties in Cosmos DB for efficient querying.
  * **Pagination**: Results are paginated with a page size of 100.
* **GetVehicleFunction**:
  * **Trigger**: HTTP (GET)
  * **Purpose**: Fetches detailed information of a vehicle using its unique ID.
  * **Implementation**: Direct lookup in Cosmos DB using the vehicle's unique ID.
* **AddVehicleFunction**:
  * **Trigger**: HTTP (POST)
  * **Purpose**: Adds a new vehicle to the database.
  * **Implementation**: Validates input data, especially ensuring the uniqueness of VIN and registration mark, before insertion into Cosmos DB.
* **UpdateVehicleFunction**:
  * **Trigger**: HTTP (PUT)
  * **Purpose**: Updates vehicle details.
  * **Implementation**: Validates changes and updates the specific vehicle entry in Cosmos DB.
* **DeleteVehicleFunction**:
  * **Trigger**: HTTP (DELETE)
  * **Purpose**: Removes a vehicle from the database.
  * **Implementation**: Deletes the vehicle entry from Cosmos DB using its unique ID.

### 💡 Relevant Code Examples

* [**Example Exception Handling Middleware**](../../code-examples/example-exception-handling-middleware.md)
* [**Example 'Auditing' Request Filters**](../../code-examples/example-auditing-request-filters.md)

### API Versioning Strategy

The primary objective of the API versioning strategy chosen, is to maintain stable API contracts and existing functionality for clients, while facilitating iterative feature development and necessary changes to backend functionality.

#### **Accept Header Transformation with APIM:**

On the client side, clients will specify the desired API version using the `Accept` header, in the format `Accept: <CONTENT_TYPE>; version=<MAJOR>.<MINOR>`. Azure API Management (APIM) is then responsible for internally managing the transformation of this request header, routing it to the correct version of the backend API using versioned URI paths.

#### Implementation Overview:

1. **Client Interface**: Clients use the `Accept` header to specify API versions as described above, e.g., `Accept: application/json; version=1.0`. This abstracts versioning away from the URI and insulates clients from potential backend URI structure changes. If backend URI paths change due to version updates or other modifications, client integrations remain uninterrupted as they rely on the consistent `Accept` header for versioning and not a specific URI path.
2. **Backend Development**: APIM transforms incoming requests and routes them to the appropriate backend API endpoint using URI based versioning. This enables the backend to be structured with explicit versioned URIs (e.g., `/v1/vehicles`, `/v2/vehicles`), which allows developers to directly relate endpoints to specific API versions. This clear delineation aids in troubleshooting, isolating changes to specific versions, and avoids unintentional impacts on other versions of the API.

### Design Rationale

#### **API Versioning Rationale:**

* **Stable Client Experience**: By leveraging the `Accept` header for versioning, clients avoid the intricacies of URI-based versioning. This reduces the likelihood of integration issues caused by URI changes.
* **Backend Clarity**: Retaining URI versioning for developers provides a transparent version management system, streamlining development and ensuring clear distinctions between API versions.

#### Directory Structure Rationale

* **Ease of Maintenance**: By collocating all related code, it simplifies the extension and maintenance of a feature.
* **Improved Discoverability**: New developers on the development team can quickly discern the system's capabilities merely by looking at the Features directory.
* **Scalability**: The addition of new features involves merely appending new folders, leaving existing features undisturbed.

#### Project Design Rationale

* **Function Separation**: Dedicating distinct Azure Functions to separate operations adheres to the Single Responsibility Principle (SRP), streamlining both management and debugging.
* **Usage of DTOs**: DTOs enable the shaping of API responses without modifying the domain model, and this intermediary layer prevents the unintentional exposure of data or internal implementation details.
* **Centralised Error Handling**: A uniform error response facilitates a smoother developer experience for those interfacing with the API.
* **Validation**: A tight feedback loop, made possible by using request-level validation, mitigates issues relating to poor data quality by capturing issues before they are able penetrate the domain layer, sending actionable error messages back to the client.

### Summary

The API layer plays a crucial role in mediating communication between the client and server. Rather than engaging in complex domain logic, this layer prioritises structuring data for exchange, promptly processing requests, and returning consistent responses. By integrating selected DDD concepts and focusing on clear request/response handling, validation, and error handling, the API provides a reliable, user-oriented, and maintainable data exchange interface.
