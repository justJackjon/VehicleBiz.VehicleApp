# Bill of Materials

## Comprehensive Bill of Materials for the Proposed Vehicle Data Management API

### **Introduction**

This 'Bill of Materials' provides a comprehensive list of all resources, both tangible and intangible, that are essential for the successful deployment and functioning of the proposed system. This includes the hardware and infrastructure, software and tools, third-party services, and associated licensing and cost details.

### **Hardware/Infrastructure**

1. **Azure Function App (Consumption Plan)**: Serverless compute service that scales automatically based on the workload. No dedicated hardware is required as Azure manages the infrastructure.
2. **Azure Cosmos DB**: Globally distributed NoSQL database service. The underlying hardware is abstracted and managed by Azure.
3. **Azure Blob Storage**: Object storage solution for the cloud, suitable for storing unstructured data like documents, logs, and media.
4. **Networking**: Because the proposed demonstration application utilises lower pricing tiers, advanced networking capability is unfortunately not available to us. However, conceivably the system may leverage Azure's virtual networking capabilities in future iterations to ensure secure and efficient communication between services. Includes features such as Virtual Networks (VNets) and Network Security Groups (NSGs).

### **Software & Tools**

1. **Azure Functions Runtime**: The runtime environment enabling the development and execution of the serverless function app.
2. **Azure Cosmos DB SQL API**: The interface for interacting with our Cosmos DB instance.
3. **Entity Framework Core**: An ORM tool to facilitate interactions between the application and the database.
4. **Application Insights SDK**: Integrated into the application for telemetry and monitoring purposes.
5. **Azure Key Vault SDK**: To interact and retrieve secrets from Azure Key Vault.
6. **GitHub Actions**: For CI/CD automation.
7. **Terraform**: Infrastructure as Code tool to provision and manage Azure resources.
8. **Swagger/OpenAPI**: For API documentation.
9. **`jq`**: A lightweight command-line JSON processor, utilised within our migration scripts to parse and manipulate JSON data.

**Third-party Services**

1. **GitHub**: Source code repository and CI/CD automation via GitHub Actions.
2. **Dependabot**: Integrated into GitHub, Dependabot scans and notifies of vulnerabilities or malware in project dependencies.
3. **Terraform Provider for Azure**: Enables Terraform to work with Azure resources.

**Licensing & Costs**

1. **Azure Function App (Consumption Plan)**: Billing is based on the total number of executed function runs and execution duration.
2. **Azure Cosmos DB**: Using the SQL API ensures eligibility for Azure's serverless pricing model. Costs are determined based on throughput measured in [Request Units (RUs)](https://learn.microsoft.com/en-us/azure/cosmos-db/request-units) and amount of consumed storage utilised.
3. **Azure Blob Storage**: Costs are associated with the amount of data stored and operations performed.
4. **GitHub**: Public repositories are free. GitHub Actions usage incurs costs after a certain number of free minutes (2,000 free at the time of writing) are consumed.
5. **Terraform**: Open-source and free. However, advanced features via Terraform Cloud or Terraform Enterprise come with costs.
6. **Entity Framework Core**: Open-source and free to use.
7. **`jq`**: Open-source and free to use (MIT license).
8. **Application Insights**: Costs are based on the volume of telemetry data and retention period.
9. **Azure Key Vault**: Pricing is based on operations and the type of secured keys or secrets.

**Summary**\
The Bill of Materials serves as a detailed inventory of all components required for the proposed system. This ensures that any potential stakeholders have a clear understanding of the resources, tools, and associated costs, allowing for informed decision-making, budgeting, and future planning for the system's lifecycle. As a living document, it is crucial to revisit and update this list to reflect the current state of the system's architecture and associated costs.
