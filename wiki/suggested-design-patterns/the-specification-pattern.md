# The Specification Pattern

## Implementing the Specification Pattern in the Proposed Vehicle Data Management API

### Introduction

The Specification Pattern encapsulates selection logic for items in a collection into distinct objects. This design pattern emphasises defining queries in an object-oriented manner, streamlining filtering logic and improving composability.

### Core Purpose

1. **Separate Criteria**: The Specification Pattern decouples filtering logic from the method or object that employs it.
2. **Reusability**: Avoids duplicating code by reusing criteria logic across multiple application areas.
3. **Composability**: Enables the combination of various specifications into more intricate ones via logical operations such as AND, OR, and NOT.

### Basic Structure

* **Specification Interface**: Usually equipped with a method named `IsSatisfiedBy`, this interface discerns whether a particular item fulfils the criteria outlined by the specification.
* **Concrete Specifications**: These are the classes that implement the specification interface and detail the actual logic of the criteria.

### Example: Vehicle Domain

Considering the need to filter vehicles based on their manufacturer or color, the Specification Pattern can be utilised instead of embedding this logic directly within our repository or service.

#### Define the ISpecification interface:

```csharp
public interface ISpecification<T>
{
    bool IsSatisfiedBy(T item);
}
```

#### Concrete Specifications Implementation:

```csharp
public class VehicleByManufacturerSpecification : ISpecification<Vehicle>
{
    private readonly string _manufacturer;

    public VehicleByManufacturerSpecification(string manufacturer)
    {
        _manufacturer = manufacturer;
    }

    public bool IsSatisfiedBy(Vehicle vehicle)
    {
        return vehicle.Manufacturer == _manufacturer;
    }
}

public class VehicleByColorSpecification : ISpecification<Vehicle>
{
    private readonly Color _color;

    public VehicleByColorSpecification(Color color)
    {
        _color = color;
    }

    public bool IsSatisfiedBy(Vehicle vehicle)
    {
        return vehicle.Color == _color;
    }
}
```

#### Utilising Specifications:

```csharp
var manufacturerSpec = new VehicleByManufacturerSpecification("Toyota");
var colorSpec = new VehicleByColorSpecification(Color.Red);

var toyotaVehicles = vehicles.Where(manufacturerSpec.IsSatisfiedBy);
var redVehicles = vehicles.Where(colorSpec.IsSatisfiedBy);
```

### Advanced Usage: Combining Specifications

The Specification Pattern can be further evolved to amalgamate multiple specifications, typically using logical operations:

#### AND Specification:

```csharp
public class AndSpecification<T> : ISpecification<T>
{
    private ISpecification<T> _left;
    private ISpecification<T> _right;

    public AndSpecification(ISpecification<T> left, ISpecification<T> right)
    {
        _left = left;
        _right = right;
    }

    public bool IsSatisfiedBy(T item)
    {
        return _left.IsSatisfiedBy(item) && _right.IsSatisfiedBy(item);
    }
}
```

### Key Benefits

* **Decoupling**: Selection logic is segregated from the client code leveraging it.
* **Reusability**: Promotes reuse of specifications across the application.
* **Composability**: Allows for the crafting of elaborate criteria by merging simpler specifications.

### Contextualising With Repositories

Within the realm of data access and repositories, specifications are invaluable for articulating query criteria in a reusable manner. Rather than using numerous repository methods for disparate query criteria, one can use a single method that ingests a specification and returns matching items.

### Summary

While introducing an additional layer of abstraction and potentially increasing complexity, the Specification Pattern proves invaluable in systems where item selection criteria are subject to frequent changes or necessitate diverse combinations. This pattern ensures enhanced flexibility, maintainability, and clarity in code structure.
