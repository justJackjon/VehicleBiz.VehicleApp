# Application Structure

## Application Structure Overview

### **Introduction**

The proposed architecture is centred around a C# .NET 6 REST API, leveraging the Azure Functions runtime and complemented by integrations with EF Core and Cosmos DB. Because the system's core responsibilities centre around straightforward CRUD operations on simplistic [vehicle data](../../appendix/sample-vehicle-data.md), a full adoption of the Domain-Driven Design (DDD) methodology, a set of design principles and patterns focusing on domain logic, isn't deemed necessary. Nonetheless, the proposed architecture selectively integrates DDD concepts where they offer value, ensuring that the system's design adheres to the fundamental principles of DDD without introducing unnecessary complexities.

### **Solution Projects**

#### **1.** [**VehicleBiz.VehicleApp.Domain**](the-domain-layer.md)

[The Domain Layer](the-domain-layer.md) forms the logical core of our system. The `Vehicle` entity is central to this layer, representing key attributes such as manufacturer, model, colour, etcetera. While the architecture does not strictly enforce Domain Driven Design (DDD), the choice of encapsulating vehicle attributes in a domain entity is influenced by DDD principles. Business logic that doesn't naturally fit within an entity is addressed by domain services, and repository interfaces, like `IVehicleRepository`, are also defined within this layer, with their concrete implementations residing in the data layer.

#### **2.** [**VehicleBiz.VehicleApp.Data**](the-data-access-layer.md)

[The Data Access Layer](the-data-access-layer.md) provides concrete implementations of repository interfaces from the Domain layer. It deals directly with database interactions through the Object Relational Mapper (ORM), Entity Framework (EF) Core. Database-specific configurations, especially tailored for Azure Cosmos DB, are maintained here.

#### **3.** [**VehicleBiz.VehicleApp.Api**](the-api-layer.md)

[The API Layer](the-api-layer.md) houses Azure Functions endpoints dedicated to specific operations. Services, repositories, and other necessary components are configured for dependency injection. Middleware functions manage tasks like error handling and logging. Additionally, this layer defines structured API models tailored to specific operations, ensuring clear communication with external systems.

#### **4.** [**VehicleBiz.VehicleApp.UnitTests**](unit-testing-component.md)

[The Unit Testing Component](unit-testing-component.md) is responsible for conducting tests on individual system elements, such as domain entities, ensuring the proper functionality of these isolated units.

#### **5.** [**VehicleBiz.VehicleApp.IntegrationTests**](integration-testing-component.md)

[The Integration Testing Component](integration-testing-component.md) ensures effective communication between different application layers, verifying that components such as the API layer and the database operate in harmony.

### **Rationale**

The chosen architecture emphasises maintainability with its clear and organised structure. By avoiding the overhead of complex architectures, it offers clarity and simplicity in design. The architecture is constructed with a clear separation of concerns, avoiding unnecessary complexity and offering adaptability for future modifications.

### **Summary**

The architectural design is tailored to efficiently manage vehicle data. With the Azure Functions runtime and Cosmos DB at its core, the application is equipped to proficiently handle CRUD operations on vehicle data. The design is both organised and adaptable, prioritising efficient data management.
