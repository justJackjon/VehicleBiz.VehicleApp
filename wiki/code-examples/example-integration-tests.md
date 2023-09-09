# Example Integration Tests

## Example integration tests using [xUnit](https://xunit.net/) and [Fluent Assertions](https://github.com/fluentassertions/fluentassertions).

#### :warning: **Note on Abstracted Details in Code Examples:**

The provided integration test examples in this wiki make use of abstracted placeholders for certain operations, particularly around the initialisation of the Cosmos DB Emulator and data seeding concerns. These placeholders, such as `CosmosDbEmulator.InitialiseContext()`, are representative of potential operations but lack specific implementation details. The purpose of these abstract representations is to focus on the test structure and logic without getting entangled in the intricacies of actual setup. When adapting these examples for real-world scenarios, these placeholders should be replaced with concrete implementations that align with their specific use case.

### **Data Access Tests**

Testing if a vehicle can be added to and then retrieved from the database:

```csharp
[Fact]
public async Task ShouldAddAndRetrieveVehicleFromDatabase()
{
    // Arrange
    // NOTE: See wiki notes above on abstracted Cosmos DB Emulator initialisation.
    var cosmosDbContext = CosmosDbEmulator.InitialiseContext();
    var vehicleRepository = new VehicleRepository(cosmosDbContext);
    var expectedVehicle = new Vehicle("ABC123");

    // Act
    await vehicleRepository.AddAsync(expectedVehicle);
    
    var retrievedVehicle = await vehicleRepository.GetByIdAsync("ABC123");

    // Assert
    retrievedVehicle.Should().NotBeNull();
    retrievedVehicle.VIN.Should().Be(expectedVehicle.VIN);
}
```

Testing if a duplicate vehicle throws the appropriate exception:

```csharp
[Fact]
public async Task ShouldThrowDuplicateVehicleException()
{
    // Arrange
    // NOTE: See wiki notes above on abstracted Cosmos DB Emulator initialisation.
    var cosmosDbContext = CosmosDbEmulator.InitialiseContext();
    var vehicleRepository = new VehicleRepository(cosmosDbContext);
    var vehicle = new Vehicle("ABC123");
    
    await vehicleRepository.AddAsync(vehicle);

    // Act & Assert
    Func<Task> act = async () => await vehicleRepository.AddAsync(new Vehicle("ABC123"));
    
    act.Should().Throw<DuplicateVehicleException>();
}
```

### **API Function Tests**

In these examples, we initialise a `DefaultHttpContext` to emulate a request and a `LoggerFactory` to create a dummy logger instance. Note that to ensure maintainability and avoid redundancy, the creation of the `LoggerFactory` can be centralised within the TestUtilities directory for reuse across multiple tests.

Testing if the `GetVehicleFunction` returns the expected vehicle details:

```csharp
[Fact]
public async Task ShouldReturnVehicleDetailsFromApiFunction()
{
    // Arrange
    // NOTE: See wiki notes above on abstracted Cosmos DB Emulator initialisation.
    var cosmosDbContext = CosmosDbEmulator.InitialiseContext();
    var vehicleRepository = new VehicleRepository(cosmosDbContext);
    var expectedVehicle = new Vehicle("ABC123", "Red");
    
    cosmosDbContext.Vehicles.Add(expectedVehicle);
    cosmosDbContext.SaveChanges();

    var function = new GetVehicleFunction(vehicleRepository);
    var context = new DefaultHttpContext();
    var request = context.Request;
    var vehicleId = "ABC123";

    // NOTE: Setup code for the ILogger dummy can/should be centralised
    var loggerFactory = LoggerFactory.Create(builder => 
    {
        builder.AddConsole();
    });
    
    var logger = loggerFactory.CreateLogger<GetVehicleFunction>();  // Replace with your actual function's type

    // Act
    var response = await function.Run(request, logger);

    // Assert
    var vehicle = (VehicleDTO)response.Value;
    
    vehicle.VIN.Should().Be(vehicleId);
}
```

Testing if the `SearchVehiclesFunction` returns the expected number of vehicles:

```csharp
[Fact]
public async Task ShouldReturnExpectedNumberOfVehiclesFromApiFunction()
{
    // Arrange
    // NOTE: See wiki notes above on abstracted Cosmos DB Emulator initialisation.
    var cosmosDbContext = CosmosDbEmulator.InitialiseContext();
    var vehicleRepository = new VehicleRepository(cosmosDbContext);

    var redVehicles = new List<Vehicle>
    {
        new Vehicle("VIN1", "Red"),
        new Vehicle("VIN2", "Red"),
        new Vehicle("VIN3", "Red")
    };

    cosmosDbContext.Vehicles.AddRange(redVehicles);
    cosmosDbContext.SaveChanges();

    var function = new SearchVehiclesFunction(vehicleRepository);
    var context = new DefaultHttpContext();
    var request = context.Request;

    // NOTE: Setup code for the ILogger dummy can/should be centralised
    var loggerFactory = LoggerFactory.Create(builder => 
    {
        builder.AddConsole();
    });
    
    var logger = loggerFactory.CreateLogger<SearchVehiclesFunction>();  // Replace with your actual function's type

    // Act
    var result = await function.RunByColor("Red", request, logger);

    // Assert
    var vehicles = (List<VehicleDTO>)result.Value;
    
    vehicles.Count.Should().Be(3);
}
```
