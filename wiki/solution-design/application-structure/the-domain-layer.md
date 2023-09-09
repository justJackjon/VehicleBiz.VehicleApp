# The Domain Layer

## Project Overview: VehicleBiz.VehicleApp.Domain

### Introduction

The domain layer primarily encapsulates the system's core business logic, repository interfaces, and domain entities. While our proposed design is not fully aligned with the principles of Domain-Driven Design (DDD), this layer does incorporate some principles of DDD, such as inversion of control, to improve the design and maintainability of the proposed application.

### Core Concepts

#### 1. Domain **Entities**

The `Vehicle` entity is the primary domain entity, designed to encapsulate the essential attributes of a vehicle. It provides a structured representation of a vehicle in accordance with the [sample dataset](../../appendix/sample-vehicle-data.md).

```csharp
public class Vehicle
{
    public string VIN { get; set; }
    public string Manufacturer { get; set; }
    public string Model { get; set; }
    public string Color { get; set; }
    public string Fuel { get; set; }
    public string Type { get; set; }
    public string RegistrationMark { get; set; }
    public DateTime RegistrationDate { get; set; }
    public int Mileage { get; set; }
    public double Valuation { get; set; }
    public List<string> Features { get; set; }
}
```

#### 2. **Repository Interfaces**

Repository interfaces, housed within the domain layer, serve as an abstraction over the concrete data access logic. They ensure the domain layer remains agnostic to specific data storage or retrieval mechanisms. This encapsulation aligns with the principle of inversion of control, where the domain outlines the contract, and [the data access layer](the-data-access-layer.md) provides the specific implementation.

```csharp
public interface IRepository<T>
{
    // Generic repository methods...
}

public interface IVehicleRepository : IRepository<Vehicle>
{
    // Vehicle-specific methods...
}
```

#### 3. **Enums**

Enumerations, specifically `Fuel` and `Type`, offer a standardised way to represent distinct sets of values related to vehicles. They ensure that specific attributes, like the type of fuel a vehicle uses or its category, are consistently defined and used throughout the application.

```csharp
public enum Fuel
{
    Gasoline,
    Electric,
    Diesel,
    Hybrid
}

public enum Type
{
    Sedan,
    SUV,
    CargoVan,
    ExtendedCabPickup,
    Wagon,
    // Other types...
}
```

#### 4. **Exceptions**

Custom exceptions offer precision in representing domain-specific errors. They allow the system to provide clear and targeted feedback. A `DuplicateVehicleException`, for example, is able to immediately communicate an attempt to register a vehicle with an already existing VIN.

```csharp
public class DuplicateVehicleException : Exception
{
    // Exception details...
}

public class VehicleNotFoundException : Exception
{
    // Exception details...
}
```

### Proposed Directory Structure

```bash
VehicleBiz.VehicleApp.Domain/
|
|-- Entities/
|   |
|   |-- Vehicle.cs
|
|-- ValueObjects/
|   |
|   |-- ...  # Placeholder; sample dataset (see appendix 3) suggests limited need.
|
|-- Interfaces/
|   |
|   |-- IRepository.cs
|   |-- IVehicleRepository.cs
|
|-- Enums/
|   |
|   |-- FuelType.cs
|   |-- VehicleType.cs
|
|-- Exceptions/
    |
    |-- DuplicateVehicleException.cs
    |-- VehicleNotFoundException.cs
    
# NOTES:
# > !! Design Choices !!:
#   Refer to the 'Design Rationale' section on this wiki page for insight into the
#   design decision to favor categorical colocation over functional cohesion for this
#   application layer.
#
# > Unified Definitions:
#   Grouping domain components by their type (e.g., entities, value objects,
#   interfaces) ensures that there is a single place for each type of domain
#   component. This centralisation helps in avoiding duplication and provides a
#   clear, logical location for new additions.
#
# > Clear Boundaries:
#   Distinct directories for entities, value objects, interfaces, etc., ensures
#   that developers can easily see all components of a particular type at a glance.
#   This categorisation can speed up navigation and enhance code readability.
#
# > Domain Integrity:
#   Given the simplicity of the data structure in the sample dataset (refer to
#   appendix 3), it is deemed that dedicated value objects may not be necessary.
#   That being said, by typically separating value objects and entities, the core
#   domain maintains its integrity. This clear separation would reinforce the DDD
#   principle of distinguishing between objects that define identity (entities) and
#   those that don't (value objects).
#
# > Extensibility:
#   As the domain grows, new categories (like aggregates or factories) can be
#   introduced as separate directories without disturbing the existing structure.
#   This ensures that the domain can evolve while preserving its organised structure.
#
# > Exception Management:
#   Having a dedicated directory for domain exceptions makes it straightforward to
#   manage and locate exceptions specific to the domain logic. This aids in creating
#   a consistent error-handling mechanism for domain-specific rules and validations.
```

### Design Rationale

#### Directory Structure Rationale

* **Functional Cohesion Decision**: While functional cohesion is highly beneficial for areas like the [API layer](the-api-layer.md) where operations and their related components are closely aligned, the domain layer of our application is relatively simple and not complex enough to warrant a fully DDD approach. Therefore, to maintain clarity and simplicity, a categorical colocation approach has been adopted in this layer, grouping related domain components by their type.

#### Project Design Rationale

* **Repository Interface Decoupling**: By situating repository interfaces within the domain, we advocate for the domain's independence from specific data access implementations. This strategic placement not only upholds the principle of inversion of control but also streamlines the integration of diverse data storage solutions without altering core domain logic.
* **Emphasising Enum Readability**: The inclusion of enumerations, such as Fuel and Type, serves dual purposes. On one hand, they highlight specific sets of domain values, offering clarity. On the other, they enhance code maintainability potential by presenting a centralised location for value-set modifications.
* **Domain-Specific Exception Clarity**: The creation and use of custom exceptions, such as `DuplicateVehicleException`, underscore a commitment to domain-specific error handling. These exceptions not only facilitate clear error feedback mechanisms but also ensure that potential domain-related issues are promptly communicated to the application's consumers.

### Summary

The domain layer incorporates specific DDD concepts to strengthen its design. By emphasising a clear representation of the business logic, the proposed project structure ensures that the core functionality is easily understood and modified. Furthermore, the conscious decision to decouple this layer from external dependencies ensures a resilient architecture, capable of evolving without succumbing to unnecessary complexities.
