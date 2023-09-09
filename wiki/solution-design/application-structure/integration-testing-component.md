# Integration Testing Component

## Component Overview: VehicleBiz.VehicleApp.IntegrationTests

### Introduction

Integration tests play a pivotal role in ensuring that individual components of the application interact seamlessly. While unit tests focus on individual units of work in isolation, integration tests verify the combined functionality of multiple components. For the `VehicleBiz.VehicleApp.IntegrationTests` project, the spotlight is on confirming the harmonious operations of the application's layers and their interactions.

### Core Concepts

#### 1. Bounded Contexts

If our system amplifies and accommodates multiple bounded contexts, integration tests become essential in validating their correct interactions.

#### 2. Infrastructure

The actual implementations of our repositories are put to test, ensuring that the ORM (Entity Framework Core) and the database (Cosmos DB) coalesce as anticipated.

#### 3. External Systems

For dependencies on third-party services or systems, integration tests verify smooth communication and data transfers between our system and external entities.

### Proposed Directory Structure

```bash
VehicleBiz.VehicleApp.IntegrationTests/
|
|-- Features/
|   |
|   |-- v1/
|   |   |
|   |   |-- SearchVehicles/
|   |   |   |-- SearchVehiclesFunctionTests.cs
|   |   |   |-- VehicleRepositorySearchTests.cs
|   |   |
|   |   |-- GetVehicle/
|   |   |   |-- GetVehicleFunctionTests.cs
|   |   |   |-- VehicleRepositoryGetTests.cs
|   |   |
|   |   |-- AddVehicle/
|   |   |   |-- AddVehicleFunctionTests.cs
|   |   |   |-- VehicleRepositoryAddTests.cs
|   |   |
|   |   |-- UpdateVehicle/
|   |   |   |-- UpdateVehicleFunctionTests.cs
|   |   |   |-- VehicleRepositoryUpdateTests.cs
|   |   |
|   |   |-- DeleteVehicle/
|   |       |-- DeleteVehicleFunctionTests.cs
|   |       |-- VehicleRepositoryDeleteTests.cs
|   |
|   |-- v2/
|   |   |-- ...  # (tests for features specific to v2)
|   |
|   |-- ... # (other versions as they evolve)
|
|-- TestUtilities/
|   |
|   |-- TestDataSeeder.cs
|   |-- IntegrationTestBase.cs
|   |-- Fixtures/
|   |   |
|   |   |-- DatabaseFixture.cs
|   |
|   |-- Collections/
|   |   |
|   |   |-- DatabaseCollection.cs
|   |
|   |-- TestData/
|       |
|       |-- VehicleTestData.cs

# NOTES:
# > Features/
#   The structure under 'Features' follows the versioned approach adopted by the main
#   API. This ensures that integration tests corresponding to a specific version of
#   the API are co-located under the respective versioned directory.
#
# > TestUtilities/
#   A centralized hub for shared testing utilities, base classes, and common
#   structures for integration tests. Ensures consistency and reduces redundancy in
#   setting up and executing tests.
```

### 💡 Relevant Code Examples

* [**Example Integration Tests**](../../code-examples/example-integration-tests.md)

### What to Test & What Not to Test

**Do Test:**

* Interactions with the database.
* API endpoints with actual database operations.
* Data consistency across operations.
* Error scenarios.

**Don't Test:**

* Scenarios covered in unit tests.
* Third-party services directly.
* Repeated scenarios.

### Design Rationale

* **Testing Genuine Interactions**: Integration tests emphasise genuine system interactions, identifying potential pitfalls that might not be evident in unit tests, like database peculiarities or ORM misconfigurations.
* **API Endpoint Testing**: Ensuring the API endpoints work as intended guarantees that from request to response, all integrated components function harmoniously.
* **Test Utilities**: Maintaining shared utilities and base classes ensures a DRY approach to testing, promoting maintainability and reducing redundancy.

### Summary

Integration tests provide a protective measure, ensuring that application components work cohesively. They concentrate on essential interactions from the [user stories](../../functional-requirements/user-stories.md), enhancing the application's stability and guard against regression.
