# Data Management & Persistence

## Data Management & Persistence Strategies for the Proposed Vehicle Data Management API

### Introduction

This document outlines the data management and persistence strategies for the vehicle data management application. The focus areas include database design, data backup, migration procedures, data integrity and validation. The design decisions, while aligning with best practices, also consider cost implications due to the proposed application's nature as a demonstration piece only.

### Sample Dataset

| Manufacturer | Model      | Color  | Fuel     | Type                | VIN               | Registration Mark | Registration Date        | Mileage | Valuation | Features                                               |
| ------------ | ---------- | ------ | -------- | ------------------- | ----------------- | ----------------- | ------------------------ | ------- | --------- | ------------------------------------------------------ |
| Chrysler     | Colorado   | orchid | Gasoline | Sedan               | D7EPHPD8STMZ89414 | GN04FDP           | 2022-10-11T00:35:24.523Z | 216906  | 73153.85  | \["Leather Seats"]                                     |
| Cadillac     | Expedition | violet | Electric | SUV                 | F6AUZSMLWANH86964 | GM47HUI           | 2010-04-28T00:17:03.533Z | 66567   | 20684.58  | \[]                                                    |
| Jeep         | Roadster   | gold   | Gasoline | Cargo Van           | 2A8X5BNE3AWD48236 | VD31ZJR           | 2014-09-17T19:48:34.023Z | 114848  | 68434.02  | \["Heated Seats","Towbar","Parking Sensors","Sunroof"] |
| Polestar     | Explorer   | plum   | Hybrid   | Extended Cab Pickup | BZ2JHLUG60S327958 | KJ41GOV           | 2009-10-21T06:24:51.242Z | 149861  | 44723.73  | \["Cruise Control"]                                    |
| Nissan       | Aventador  | purple | Gasoline | Wagon               | KT5BB2X72ZY516511 | US79EVF           | 2010-08-13T23:13:20.998Z | 105772  | 92495.43  | \["Leather Seats"]                                     |

### Database Design

Given the NoSQL nature of Cosmos DB, data will be stored in a document-based format. Our [sample dataset](data-management-and-persistence.md#sample-dataset) illustrates individual vehicles with their corresponding attributes. Considering the [user stories](../functional-requirements/user-stories.md) and the functional requirements, there isn't an immediate need for relational data structures. As such, each vehicle will be represented as an individual document within the Cosmos DB collection.

**Best Practice**: In a fully-fledged application, considerations might be made for data normalisation, creating separate collections for related entities (e.g., manufacturers, models), and establishing relationships between them to avoid data redundancy.

**Cost-driven Decision**: For this demo application, to minimise operational costs, we will store all vehicle details in a single collection, accepting a certain level of data redundancy in favor of simplicity and reduced [Request Unit (RU)](https://learn.microsoft.com/en-us/azure/cosmos-db/request-units) consumption.

### **Cosmos DB Partitioning Strategy**

The system will employ the Vehicle Identification Number (VIN) as the partition key for Cosmos DB. The choice of the VIN as the partition key offers several advantages, but also comes with its own set of challenges:

#### **Advantages**:

* **Uniqueness**: Each VIN is unique to a specific vehicle, ensuring that data is evenly distributed across partitions, avoiding hot partitions.
* **Efficient Data Access**: Queries that filter by the VIN can be directed to the specific partition where the data resides, resulting in faster query performance.
* **Scalability**: As the dataset grows, Cosmos DB can distribute these partitions across multiple physical partitions and servers, ensuring that the system remains scalable and responsive.
* **Consistent Performance**: Using the VIN, which is a frequently accessed attribute, ensures that read and write operations are spread evenly, maintaining consistent performance.

#### **Challenges**:

* **Cross-Partition Queries**: Given the [user stories](../functional-requirements/user-stories.md), there will be scenarios where users might want to search vehicles based on attributes other than VIN, like manufacturer, model, or colour. Such queries can span multiple partitions, potentially affecting performance and costing more [RUs](https://learn.microsoft.com/en-us/azure/cosmos-db/request-units).
* **Data Distribution**: While the VIN ensures uniqueness, if certain VIN ranges are accessed more frequently than others, this could lead to hot partitions.

#### **Mitigation Strategies**:

* **Indexing**: By optimising indexes for the search criteria mentioned in the [user stories](../functional-requirements/user-stories.md), such as manufacturer, model, and colour, the system can efficiently handle cross-partition queries. This ensures that even when querying across partitions, the performance remains optimal.
* **Future Caching Layer**: While not proposed within this initial design spec, introducing a caching layer in the future can significantly reduce the need to query the database directly, especially for more frequently accessed data. This can further optimise performance and reduce costs.
* **Monitoring and Adjusting**: Regularly monitoring the access patterns and making necessary adjustments, like re-partitioning or introducing new indexes, can help in maintaining consistent performance.

By being aware of these challenges and proactively planning for them, the system is designed to provide efficient and scalable vehicle data management.

### Data Backup & Recovery

Azure Cosmos DB provides automatic and frequent backups without affecting the performance or availability of the database operations. By default, it [takes backups every 4 hours](https://learn.microsoft.com/en-us/azure/cosmos-db/periodic-backup-restore-introduction#how-azure-cosmos-db-performs-data-backup).

**Best Practice**: For critical applications, it's recommended to have a custom backup strategy in place, which may include more frequent backups, storing backups in different regions, or even different cloud providers.

**Cost-driven Decision**: Given the nature of this demo application, the default backup frequency provided by Cosmos DB will suffice. This minimises additional backup storage costs while ensuring data recoverability.

### Data Migration

As this is a demonstration application, we're working with a limited dataset. Tools like Azure Data Factory could be used for large scale data migrations.

**Best Practice**: For significant migrations, using a dedicated service like Azure Data Factory ensures data integrity, provides error logging, and allows for transformations during the migration process.

**Cost-driven Decision**: Due to cost considerations and the limited size of the dataset, a [simple script-based approach](../code-examples/example-data-migration-script.md) will be used to upload the provided CSV data into Cosmos DB.

### 💡 Relevant Code examples

* [**Example Data Migration Script**](../code-examples/example-data-migration-script.md)

### **Data Integrity & Validation**

Maintaining data integrity is essential for the application's reliable operation. While Cosmos DB doesn't natively enforce schemas at the database level, our strategy ensures data is valid before persistence.

**Best Practice:** In a comprehensive application, server-side validation rules and triggers in Cosmos DB would be used to ensure only valid data is stored. This can encompass checks on data types, allowed values, or mandatory fields.

**Our Implementation:** We will be leveraging [Fluent Validation](https://docs.fluentvalidation.net/en/latest/) within our backend services to validate incoming data before any persistence operations. This approach not only ensures data integrity but also offers descriptive feedback on validation failures, enhancing the API's user experience.

### Summary

The data management strategies detailed here are tailored for the demo application's select requirements, balancing technical best practices with cost considerations inherent to a demonstration application. Despite these cost considerations, the processes and design decisions are still geared heavily towards ensuring efficiency, scalability, and data integrity.
