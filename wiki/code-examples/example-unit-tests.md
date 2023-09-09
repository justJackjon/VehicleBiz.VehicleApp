# Example Unit Tests

## Example unit tests using [xUnit](https://xunit.net/), [NSubstitute](https://nsubstitute.github.io/) and [Fluent Assertions](https://github.com/fluentassertions/fluentassertions).

### **Data Access Tests**

Testing retrieval of a vehicle by VIN:

```csharp
[Fact]
public async Task ShouldRetrieveVehicleByVIN()
{
    // Arrange
    var vehicles = new List<Vehicle> { new Vehicle("ABC123", "Red") }.AsQueryable();

    var dbSetMock = Substitute.For<DbSet<Vehicle>, IQueryable<Vehicle>>();
    ((IQueryable<Vehicle>)dbSetMock).Provider.Returns(vehicles.Provider);
    ((IQueryable<Vehicle>)dbSetMock).Expression.Returns(vehicles.Expression);
    ((IQueryable<Vehicle>)dbSetMock).ElementType.Returns(vehicles.ElementType);
    ((IQueryable<Vehicle>)dbSetMock).GetEnumerator().Returns(vehicles.GetEnumerator());

    var dbContextMock = Substitute.For<VehicleDbContext>();
    dbContextMock.Vehicles.Returns(dbSetMock);

    var repo = new VehicleRepository(dbContextMock);

    // Act
    var vehicle = await repo.GetByVIN("ABC123");

    // Assert
    vehicle.Should().NotBeNull();
    vehicle.VIN.Should().Be("ABC123");
}
```

### **API Function Tests**

Testing that the `SearchVehiclesFunction` returns vehicles of a specific colour:

```csharp
[Fact]
public async Task ShouldReturnVehiclesOfSpecifiedColor()
{
    // Arrange
    var vehicleRepository = Substitute.For<IVehicleRepository>();
    
    vehicleRepository.GetByColor("Red").Returns(new List<Vehicle>
    {
        /*... sample red vehicles ...*/
    });
    
    var function = new SearchVehiclesFunction(vehicleRepository);

    // Act
    var result = await function.RunByColor("Red");

    // Assert
    var vehicles = (List<VehicleDTO>)result.Value;
    
    vehicles.Should().AllBeEquivalentTo(new { Color = "Red" });
}
```

### Domain Entity Tests

Testing a vehicle's colour consistency:

```csharp
[Fact]
public void ShouldRetainVehicleColor()
{
    // Arrange
    var vehicle = new Vehicle("ABC123", "Red");

    // Act & Assert
    vehicle.Color.Should().Be("Red");
}
```

### Specification Tests

Testing a specification for vehicles registered after a certain date:

```csharp
[Fact]
public void ShouldMatchVehiclesRegisteredAfterGivenDate()
{
    // Arrange
    var spec = new VehicleRegisteredAfterSpecification(new DateTime(2020, 1, 1));
    var vehicle = new Vehicle("ABC123", "Red", new DateTime(2021, 1, 1));

    // Act & Assert
    spec.IsSatisfiedBy(vehicle).Should().BeTrue();
}
```
