# Performance & Scaling

## Performance & Scalability Blueprint for the Proposed Vehicle Data Management API

### **Introduction**

This document details the methodologies and strategies employed by the vehicle data management API to ensure optimal performance and scalability. Emphasis is placed on the application's design decisions to handle various workloads and user demands.

### **Scalability**

Scalability is a core feature of any modern application, especially when dealing with unpredictable workloads. Our application on Azure ensures that it meets these demands without compromising on performance.

* **Vertical Scaling:** Azure Functions, while primarily designed for consumption-based usage, do offer the flexibility to switch to a premium plan. This switch would allow for increased resources when the demand necessitates it, offering more CPU or memory to the application, ensuring it doesn't lag under heavy loads.
* **Horizontal Scaling:** A major benefit to cloud-native applications is their ability to expand horizontally. Azure Functions, forming the core of our application's logic, scale out automatically based on the number of incoming requests. This ensures that during peak times, new instances can be spun up dynamically to cater to the increased demand, and similarly, during quiet periods, unnecessary instances can be terminated.

### **Load Balancing**

To ensure consistent application performance, incoming traffic needs to be efficiently distributed among available resources.

* **Azure Functions:** In our consumption plan, Azure inherently balances the load. It does this by distributing incoming requests evenly across function instances. This ensures that no single instance is overwhelmed, maintaining a consistent response time.
* **Azure Cosmos DB:** On the data side, Cosmos DB handles the load by auto-managing partitioning. As data grows, it's distributed across various physical partitions, ensuring that read and write operations do not degrade in performance. The application leverages this by using the VIN as a logical partition key, ensuring that data access remains optimal regardless of the dataset's size. For a detailed breakdown of the advantages, challenges, and mitigation techniques of our intended [Cosmos DB Partitioning Strategy](data-management-and-persistence.md#cosmos-db-partitioning-strategy), refer to the [Data Management & Persistence](data-management-and-persistence.md) wiki page.

### Caching Strategies

Caching plays a pivotal role in enhancing the application's performance by reducing data access times and ensuring responsiveness.

* **Using HTTP for Caching**: Our REST API is designed to exploit native HTTP caching mechanisms. When a client issues a request, it can incorporate headers, such as `If-None-Match`, paired with the ETag of its current version. Utilising the ETag, the server can ascertain whether the client's version is up-to-date. If the client's version matches the server's, a `304 Not Modified` status is dispatched, guiding the client to employ its cached version. This approach not only conserves bandwidth but also bolsters the application's overall responsiveness. For write-oriented operations, like PUT or DELETE, the `If-Match` header is instrumental in ensuring that clients interact with the most recent resource version. If there's a mismatch, a `412 Precondition Failed` status is dispatched.
* **Cosmos DB and ETags**: Cosmos DB natively supports ETags for its documents. With every document update, Cosmos DB autonomously refreshes the respective ETag. This inherent feature is leveraged by our REST API to realise the HTTP caching mechanism previously described. Synchronising directly with Cosmos DB's ETags aligns flawlessly with our strategy of employing HTTP for caching, establishing an efficient process to synchronise client data.
* **Azure Cosmos DB TTL**: We will additionally integrate the Time-to-Live (TTL) feature of Cosmos DB into our strategy. By assigning a TTL to specific items, we can streamline data management. Data that becomes obsolete or less relevant over time is auto-expired, ensuring the database operates efficiently and remains agile.
* **Future Caching Implementations**: Looking ahead, a plausible enhancement to our architecture could be the inclusion of Azure Cache for Redis. This in-memory data structure store, Redis, when integrated, can cache data that's accessed frequently, further amplifying the speed of our application's data retrieval operations.

### **Performance Testing**

Ensuring the system performs under varied conditions is crucial. Performance testing does not solely assess the system's speed or responsiveness; it evaluates how the system behaves under different scenarios, gauging its robustness, reliability, and scalability.

* **Tools:** Continuous monitoring is achieved through Azure Monitor and Application Insights. These tools provide invaluable insights, highlighting potential bottlenecks or areas that might benefit from optimisation.
* **Methodologies:** The application will undergo regular testing to ensure its robustness. This includes proposed stress testing (to gauge its performance under extreme conditions), load testing (to understand its behaviour under expected loads), and spike testing (to see how it handles sudden, unexpected increases in traffic).

### **Summary**

The vehicle data management application's architecture is designed with a strong emphasis on performance and scalability. Leveraging Azure's diverse capabilities, from auto-scaling Azure Functions to efficient data partitioning with Cosmos DB, the system is designed to deliver consistent performance across a spectrum of scenarios.
