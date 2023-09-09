# Unit Testing Component

## Component Overview: VehicleBiz.VehicleApp.UnitTests

### Introduction

The `VehicleBiz.VehicleApp.UnitTests` project serves as the foundation for ensuring the robustness and reliability of our system. By leveraging key concepts from Domain-Driven Design (DDD) and testing best practices, this project seeks to validate both domain logic and API functionality in isolation.

### Core Concepts

#### 1. Ubiquitous Language

Even within our testing realm, it's crucial to maintain a consistent language reflecting the domain. This ensures test names and their underlying logic resonate with the domain's terminologies.

#### 2. Rich Domain Model Testing

Here, we focus on verifying the behaviours of domain entities, value objects, aggregates, and domain services.

#### 3. Mocking Repositories

By leveraging mocking tools, we can simulate repository interactions, enabling us to test domain and application services without actual database interactions.

#### 4. Testing Invariants

These tests ensure the consistent enforcement of business rules, known as invariants, within our domain.

### Proposed Directory Structure

```bash
VehicleBiz.VehicleApp.UnitTests/
|
|-- Domain/
|   |
|   |-- VehicleTests/
|   |   |
|   |   |-- EntityTests.cs
|   |   |-- ValueObjectTests.cs
|   |   |-- SpecificationTests.cs
|   |   |-- FactoryTests.cs
|
|-- Api/
|   |
|   |-- v1/
|   |   |
|   |   |-- Features/
|   |   |   |
|   |   |   |-- SearchVehicles/
|   |   |   |   |-- SearchVehiclesFunctionTests.cs
|   |   |   |
|   |   |   |-- GetVehicle/
|   |   |   |   |-- GetVehicleFunctionTests.cs
|   |   |   |
|   |   |   |-- AddVehicle/
|   |   |   |   |-- AddVehicleFunctionTests.cs
|   |   |   |
|   |   |   |-- UpdateVehicle/
|   |   |   |   |-- UpdateVehicleFunctionTests.cs
|   |   |   |
|   |   |   |-- DeleteVehicle/
|   |   |       |-- DeleteVehicleFunctionTests.cs
|   |
|   |-- v2/
|   |   |
|   |   |-- Features/
|   |   |   |-- ...  # (tests for features specific to v2)
|   |
|   |-- ... # (other versions as they evolve)
|
|-- DataAccess/
|   |
|   |-- Features/
|   |   |
|   |   |-- VehicleData/
|   |       |-- VehicleRepositoryTests.cs
|   |       |-- SpecificationTests.cs
|
|-- TestUtilities/
|   |
|   |-- Mocks/
|   |   |
|   |   |-- RepositoryMocks.cs
|   |
|   |-- Fixtures/
|   |   |
|   |   |-- VehicleFixture.cs
|   |
|   |-- Collections/
|   |   |
|   |   |-- VehicleCollection.cs
|   |
|   |-- TestData/
|       |
|       |-- VehicleTestData.cs
    
# NOTES:
# > Domain/
#   Contains tests focused on domain logic, including entities, value objects,
#   specifications, and factories. This domain logic should be consistent across
#   versions of the API. Even if the API representation changes between versions,
#   the core domain logic shouldn't. Therefore, there's no need to version tests for
#   domain logic.
#
# > Api/
#   Houses tests for the API layer. The structure is broken down by API versions
#   (e.g., v1, v2), ensuring that tests for each version are kept separate and
#   organised. Within each version directory, the feature-centric structure is
#   preserved.
#
# > DataAccess/
#   Represents tests for the Data Access Layer, again reflecting its feature-centric 
#   organisation. Note that the way data is accessed, stored, or manipulated in the
#   backend should also remain consistent. Even if the API evolves to provide more
#   functionality, the underlying data access mechanisms remain largely unchanged.
#   Hence, no versioning here makes sense.
#
# > TestUtilities/
#   Central hub for shared testing utilities:
#   - Mocks/
#     Contains mock objects used across tests, providing consistent and controlled
#     test scenarios.
#   - Fixtures/
#     Houses shared setup and teardown functionalities for grouped tests.
#   - Collections/
#     Used when tests might share a stateful resource.
#   - TestData/
#     Stores various test scenarios for validating methods with multiple inputs.
```

### 💡 Relevant Code Examples

* [**Example Unit Tests**](../../code-examples/example-unit-tests.md)

**Do Test:**

* Business rules and invariants.
* Edge cases and boundary conditions.
* Public methods with embedded logic.
* Scenarios involving error handling and exceptions.

**Don't Test:**

* Third-party code.
* Simple getter and setter methods with no logic.
* Direct interactions with external services (utilise mocks instead).
* The database (consider using an in-memory database or mocks for better test isolation).

### Design Rationale

* **Domain and API Test Segregation**: This separation ensures that domain logic is validated independently, in line with Domain Driven Design principles, regardless of whether a fully compliant DDD architecture is implemented.
* **Mock Usage**: Using mocks streamlines testing, eliminating reliance on external components, thus enhancing test speed and reliability.
* **Descriptive Test Naming**: Clear test names serve as self-documentation, promoting clarity and maintainability.
* **Testing Focus**: Concentrating on business rules, boundaries, and logic ensures our system adheres to its specifications.

### Summary

Unit testing is essential for evaluating the functionality of individual components within our system. By incorporating DDD concepts and established testing practices, we not only enhance the system's reliability to meet current specifications but also equip it to address future functional requirements.
