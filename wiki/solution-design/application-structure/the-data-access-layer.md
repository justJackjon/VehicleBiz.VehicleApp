# The Data Access Layer

## Project Overview: VehicleBiz.VehicleApp.Data

### Introduction

The `VehicleBiz.VehicleApp.Data` project is centred around data access. While the proposed architecture leans towards simplification, there are select DDD concepts that can enhance its design and maintainability.

### Core Concepts

#### 1. [**Repository Pattern**](../../suggested-design-patterns/the-repository-pattern.md)

Even though the repository interfaces reside in the domain layer, the actual implementation of these repositories finds its place in the Data project. This pattern provides an abstract set of domain-centric operations, ensuring the encapsulation of data access logic.

```csharp
public class VehicleRepository : IVehicleRepository
{
    // Implementation of CRUD operations and other domain-centric methods...
}
```

#### 2. [**Specification Pattern**](../../suggested-design-patterns/the-specification-pattern.md)

Given the user stories' diverse search criteria, this pattern is instrumental in encapsulating vehicle querying logic based on different criteria. This approach organises the querying logic, promoting reusability without overloading the repository.

```csharp
public class VehicleByManufacturerSpecification : ISpecification<Vehicle>
{
    // Implementation to filter vehicles by manufacturer...
}
```

#### 3. **Configuration**

This encompasses classes related to configuring Azure Cosmos DB and potential transformations between domain entities and database models.

```csharp
public class CosmosDbSettings
{
    public string ConnectionString { get; set; }
    // Other configuration properties...
}

public class MappingProfile : Profile
{
    // AutoMapper configuration for transformations:
    // Domain-to-Database, Database-to-Domain, Domain-to-DTO, etc.
}
```

#### 4. **Cosmos DB Configuration and Adjustments**

Although Entity Framework Core supports migrations, Cosmos DB, being a schema-less NoSQL database, handles migrations differently from relational databases; thus, traditional migrations aren't applicable here.

```csharp
// Sample code to demonstrate how EF Core might create containers if they don't exist.
public class VehicleDbContext : DbContext
{
    public DbSet<Vehicle> Vehicles { get; set; }
    
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseCosmos("<connectionString>", "<databaseName>");
    }
}
```

* **Container (Table) Creation**: EF Core can be configured to auto-create containers in Cosmos DB during development.
* **Handling Schema Variations**: Since Cosmos DB is schema-less, changes to the data model in the application might require the application logic to handle potential schema differences in stored JSON documents.
* **Throughput and Indexing Adjustments**: Any modifications to throughput or indexing would be managed outside of EF Core, either through custom scripts, the Azure portal, or Azure CLI. It would be preferable to keep track of these changes, perhaps with documentation or scripts within a "`CosmosDbAdjustments/`" directory, as proposed in the diagram below.

### Proposed Directory Structure

The proposed structure for `VehicleBiz.VehicleApp.Data` is:

```bash
VehicleBiz.VehicleApp.Data/
|
|-- Vehicles/
|   |
|   |-- Repositories/
|   |   |
|   |   |-- VehicleRepository.cs
|   |
|   |-- Specifications/
|       |
|       |-- VehicleByManufacturerSpecification.cs
|       |-- VehicleByModelSpecification.cs
|       |-- ...  # (other vehicle-related specification classes)
|
|-- Configuration/
|   |
|   |-- CosmosDbSettings.cs
|   |-- MappingProfile.cs  # (if using a tool like AutoMapper)
|
|-- CosmosDbAdjustments/
|   |
|   |-- Documentation/
|   |   |
|   |   |-- ThroughputAdjustments.md
|   |   |-- IndexingAdjustments.md
|   |
|   |-- Scripts/
|       |
|       |-- IndexingPolicyUpdateScript.sh
|       |-- ...  # (other Cosmos DB management scripts)

# NOTES:
# > Vehicles/
#   The vehicles directory centralises all data access concerns related to vehicle
#   management. This makes it easier to locate vehicle-specific data operations and
#   specifications.
#   
# > Configuration/
#   The configuration directory remains at the root level as it pertains to global
#   configuration settings that are applicable across all functional areas.
# 
# > CosmosDbAdjustments/
#   As adjustments to Cosmos DB might be applicable across multiple functionalities, 
#   the CosmosDbAdjustments directory remains outside of specific functional
#   categories.
#   - Documentation/
#     The documentation subdirectory houses markdown files or other documentation
#     detailing any manual adjustments made to Cosmos DB, such as changes to
#     throughput or indexing policies.
#   - Scripts/
#     The scripts subdirectory contains scripts (potentially in bash script or other
#     scripting languages) to manage specific Cosmos DB configurations or
#     adjustments, such as updating indexing policies.
```

### Design Rationale

#### Directory Structure Rationale

* Adopting a functional cohesion approach for the Data Access Layer aligns the structure with both the domain's organisation and the [API Layer](the-api-layer.md)'s design, ensuring consistent and intuitive navigation across layers. By mirroring the API Layer's feature-based organisation, developers can effortlessly trace operations from the API endpoint down to data persistence, leading to an enhanced understanding of the application flow. This format also isolates data operations by domain functionality, streamlining any future expansions or modifications. As the Data Access Layer specifically deals with persistence, this organisation further clarifies which repository or specification is associated with which domain concern, simplifying debugging and maintenance.

#### Project Design Rationale

* [**Repository Pattern**](../../suggested-design-patterns/the-repository-pattern.md): This pattern's adoption ensures that the data access logic remains abstracted, paving the way for future flexibility in changing data storage mechanisms without affecting other layers.
* [**Specification Pattern**](../../suggested-design-patterns/the-specification-pattern.md): Given the multifaceted search criteria, this pattern bestows a tidier organisation to the querying logic, ensuring reusability and a streamlined repository implementation.
* **Distinct Configuration**: By keeping configurations distinct, any modifications to settings or database specifics won't inadvertently influence the repository or other data access logic.

### Summary

By incorporating these specific DDD concepts into the Data Access Layer, we achieve a structured, scalable data access approach that aligns seamlessly with our solution's simpler architectural requirements.
