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

Selecting an appropriate partition key is crucial in ensuring optimal performance and cost-efficiency in Cosmos DB. The chosen partition key should facilitate the most frequent access patterns of the application while also ensuring a balanced distribution of data. Based on the provided [user stories](../functional-requirements/user-stories.md) and the [analysis of the sample dataset](../appendix/vehicle-manufacturer-distribution.md), **Manufacturer** emerges as a suitable partition key, aligning well with anticipated search behaviours and providing an even data distribution. The advantages, challenges and mitigation strategies related to this decision are all detailed below.

#### **Dataset Analysis**:

The dataset dataset indicates a fairly even distribution of vehicles among different manufacturers and vehicle types. A detailed breakdown of [manufacturer distribution](../appendix/vehicle-manufacturer-distribution.md) and [vehicle type distribution](../appendix/vehicle-type-distribution.md) are both available in the appendix.

#### **Advantages**:

- **Efficient Data Access**: Queries that filter by manufacturer, a key search criterion based on the user stories, can be directed to the specific partition where the data resides, resulting in faster query performance.
- **Scalability**: As the dataset grows, Cosmos DB can distribute these partitions across multiple physical partitions and servers, ensuring that the system remains scalable and responsive.
- **Consistent Performance**: The [even distribution](../appendix/vehicle-manufacturer-distribution.md) of manufacturers in the dataset ensures that read and write operations are spread evenly across partitions.
- **Alignment with User Stories**: Given the user stories, many operations revolve around properties of vehicles, with manufacturer being a primary attribute of interest.

#### **Challenges**:

- **Cross-Partition Queries**: Users might want to search vehicles based on attributes other than the manufacturer. Such queries can span multiple partitions, potentially affecting performance and incurring higher [RU costs](https://learn.microsoft.com/en-us/azure/cosmos-db/request-units).
- **Unknown Usage Patterns**: Without historical usage data, it is challenging to predict the most common access patterns. It is therefore uncertain if users will prioritise manufacturer (brand loyalty) or vehicle type (utilitarian approach) in their searches.

#### **Mitigation Strategies**:

- **Indexing**: Optimising indexes for other common search criteria, such as model and colour, can help efficiently manage cross-partition queries.
- **Future Caching Layer**: While not proposed within this initial design spec, introducing a caching layer in the future can significantly reduce the need to query the database directly, especially for more frequently accessed data. This can further optimise performance and reduce costs.
- **Monitor & Adjust**: Regularly monitor access patterns and adjust as necessary, such as introducing new indexes or even reconsidering the partition key based on observed user behaviour.
  
#### **Note on Assumptions and Future Adjustments**:

The current decision is rooted in the analysis of the available dataset and assumptions about user behaviour. In the real world, post-launch, a system would closely monitor usage patterns. Depending on the observed query patterns, one might reconsider the partitioning strategy. While adjusting a partitioning strategy post-launch presents challenges, especially in live environments, early adjustments can be more manageable than later ones.

In this demo application, assumptions are made due to a lack of real-world usage data. The decision to choose the manufacturer as the partition key is based on the hypothesis that consumers might favour brand loyalty. However, it is equally plausible that they might lean towards a more utilitarian approach, basing purchasing decisions on functional requirements like vehicle type. Only with real-world user data can a fully informed decision be made.

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
