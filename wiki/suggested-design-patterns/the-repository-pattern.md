# The Repository Pattern

## Implementing the Repository Pattern in the Proposed Vehicle Data Management API

### Introduction

The Repository Pattern is a design pattern that offers an abstraction layer between the data access layer and the business logic or domain layer of an application. By centralising data or web service access logic, it allows for greater maintainability, flexibility, and testability.

### Core Benefits

* **Decoupling**: Ensures the domain layer is isolated from data access specifics, upholding the Single Responsibility Principle.
* **Testability**: Facilitates simpler unit testing of the domain layer using mock repositories.
* **Flexibility**: Switching data storage mechanisms becomes less disruptive to the domain layer.
* **Consistent Data Access Rules**: Centralising data access facilitates consistent application of rules like caching, logging, or validation.

### Basic Structure

1. **Repository Interface**: Defines the contract for the repository, detailing available methods for data access.
2. **Concrete Repository**: Implements the repository interface and houses the actual data access logic.

### Application to the Vehicle Domain

#### Define Repository Interface:

```csharp
public interface IVehicleRepository
{
    Task<IEnumerable<Vehicle>> GetAllAsync();
    Task<Vehicle> GetByIdAsync(string id);
    Task AddAsync(Vehicle vehicle);
    Task UpdateAsync(Vehicle vehicle);
    Task DeleteAsync(string id);
    // ... other methods for more intricate queries or operations.
}
```

#### Concrete Repository Implementation:

Using Cosmos DB as the data storage:

```csharp
public class CosmosVehicleRepository : IVehicleRepository
{
    private readonly CosmosClient _cosmosClient;
    private readonly Container _container;

    public CosmosVehicleRepository(CosmosClient cosmosClient, string databaseName, string containerName)
    {
        _cosmosClient = cosmosClient;
        _container = _cosmosClient.GetContainer(databaseName, containerName);
    }

    public async Task<IEnumerable<Vehicle>> GetAllAsync()
    {
        var query = _container.GetItemQueryIterator<Vehicle>();
        List<Vehicle> results = new List<Vehicle>();
        while (query.HasMoreResults)
        {
            var response = await query.ReadNextAsync();
            results.AddRange(response.ToList());
        }
        return results;
    }

    public async Task<Vehicle> GetByIdAsync(string id)
    {
        try
        {
            ItemResponse<Vehicle> response = await _container.ReadItemAsync<Vehicle>(id, new PartitionKey(id));
            return response.Value;
        }
        catch (CosmosException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            return null;
        }
    }

    public async Task AddAsync(Vehicle vehicle)
    {
        await _container.CreateItemAsync(vehicle, new PartitionKey(vehicle.Id));
    }

    public async Task UpdateAsync(Vehicle vehicle)
    {
        await _container.UpsertItemAsync(vehicle, new PartitionKey(vehicle.Id));
    }

    public async Task DeleteAsync(string id)
    {
        await _container.DeleteItemAsync<Vehicle>(id, new PartitionKey(id));
    }
}
```

### Key Considerations

* **Generic Repositories**: Implementing a generic repository (e.g., `IRepository<T>`) can minimise redundancy. However, it may not accommodate all scenarios, particularly when entities demand unique methods or behaviours.
* **Query Logic**: In more intricate applications or those leaning towards DDD, it's beneficial to segregate query logic from the repository, possibly using patterns like the [Specification Pattern](the-specification-pattern.md).

### Summary

In summary, the Repository Pattern serves as a key architectural element that isolates data retrieval and storage details from the rest of the application. For the Vehicle API, it offers a unified and consistent approach to handling vehicle-related operations, making the data source interchangeable without impacting the domain layer. The outcome is a codebase that is easier to manage, extend, and test.
