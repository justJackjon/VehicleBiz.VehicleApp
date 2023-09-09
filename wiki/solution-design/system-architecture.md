# System Architecture

## System Architecture Overview&#x20;

### Introduction

To demonstrate the system's capability to fulfil the [acceptance criteria](../functional-requirements/user-stories.md), this document describes the design decisions made to accommodate the requirements of its hypothetical stakeholders, which include 'Car Buyers', 'Sales Representatives', and 'Inventory Managers'. Consequently, the design leverages Azure's cloud-native services to ensure robust data management, scalability, and reliability. It is important to note that the stakeholders and [user stories](../functional-requirements/user-stories.md) presented are fictional, serving purely to showcase the capabilities of the application.

The proposed architecture embodies a fully cloud-native approach, built on Microsoft Azure and engineered to manage and organise [vehicle data](../appendix/sample-vehicle-data.md) as per the [functional requirements](../). The proposed solution exposes a REST API backed by a cloud-native NoSQL database, and includes identity services, a reverse proxy layer, and monitoring tools to address authentication, API management and operational readiness concerns. Further details regarding the specifics of each major component of the design are included below.

### System Architecture Diagram

<figure><img src="https://mermaid.ink/img/pako:eNp9lFFP2zAQx7-K5UnTpoE22GAlD5McO6EVVKuari_rHkzqFovEzpwEwYDvvrPjhLqDPSV3-d0_d-c7P-BcrwWO8Nbw6hpdzlcKobq96swVJn9aI9C7iWqEUbx4v8IWQIikP_uPaavyRmpVr_CvKIqcc_BRXWjThVAWQwzVdalrxOKO7kwW73BkYqWrqpA5txpoomq5vW56_arqHTtBcQZBcaGvUNZow7eig63HO3bgi-WQ_IW4R0veFk3Hg-msARZqvdeR5K5rBcqEuZW5qPuWnI9B9Vw24_YKxF4O9u3sNYZ2ZjOI9YJoZqTKZcWLLifvHry7rZpNpkMpYKApV1BqKZSvxwK7PGHPOBzQrUBMGpFDh-5RfEx9EGH_qZ8W0sl7RWoF4c8UDrsthQlLJxQdHn57XOHxYjFzGc7F71bUEP_osncQPD02F42RAtIa67qxh2O5i-Ue5cS8EPoQsCR1bOpJOv_B0PdKGO4n9NGOYZAZaZtrKMgPm9MgzBGEeWShb0T3hQbqU60ktE6qLXqLLvV2C28Om1jsfOyxbiZcwyEHRCcfKbNYNguwhTCGb7QpYYR5I56JbPY6EWcB8dqvfFueW-hKQkth5ObfyvOC1zUTG_TCLqONLIroDUnYaXoUwMEqeyz9yuhpGmru7a8nR8fJJ0ICcn95e82jsy-jkAzW1mNxPEppEmAvblL__xN6lJyFmfbL45FkxEiyV4xfFU-wk_Qk-YwPMCxCyeUa7tUHy68wzFgJBxbB65qbG7s-T8DxttHZvcpx1JhWHOC2WsPBMslh20ocbXhRD95kbYdtcApnTrvb213iT38BhX7kCA?type=png" alt=""><figcaption><p>Vehicle Data Management System Architecture Diagram</p></figcaption></figure>

### System Components

#### **1. Azure Function App**

A REST API, developed on the Azure Functions runtime, will offer cost-efficiency while addressing scalability concerns. On a consumption-based App Service plan, charges are based on execution time and number of executions. Access to the Function App will be network restricted, allowing only the Azure API Management instance to invoke the functions in a non-development environment. As an added layer of security, a specific host-level key, stored in API Management's 'Named Values' store, and synchronised with Azure Key Vault, is required to make successful requests to our backend HTTP endpoints. For more information about this layer of the application, visit [The API Layer wiki page](application-structure/the-api-layer.md).

#### **2.** Azure Cosmos DB Integration

The core of our data storage solution is Azure Cosmos DB, a multi-model database service tailored for scalability and distributed applications. The SQL API model will be utilised for the proposed application, as amongst other design considerations outlined in the [Data Management & Persistence wiki page](data-management-and-persistence.md), it is especially suitable for serverless configurations. The system will host a singular 'vehicles' collection, optimised for rapid access and data retrieval. The decision to use the Vehicle Identification Number (VIN) as the partition key is a [strategic](data-management-and-persistence.md#cosmos-db-partitioning-strategy) one. This design choice, along with its advantages and associated challenges, is elaborated on further in the [Cosmos DB Partitioning Strategy](data-management-and-persistence.md#cosmos-db-partitioning-strategy) section.

#### **3.** Azure Active Directory B2C (AAD B2C)

The commitment to a suitably secure identity solution is underpinned by the integration of AAD B2C. Tailored specifically for customer-facing applications, AAD B2C offers a seamless and secure authentication service. JWT tokens, issued by AAD B2C, will act as the backbone of the authentication process. Beyond just authentication, these tokens also play a crucial role in authorisation. The claims embedded within these tokens will define user roles and permissions, ensuring that users can access only the data and features they are entitled to. Further, it is important to note that AAD B2C makes up just one layer of the system's 'defence in depth' approach, which is described in more detail on the [Application Security wiki page](application-security.md).

#### **4. Azure API Management**

Azure API Management is designed to serve as the external gateway between clients and the system’s backend services, underlining its importance in the overall architecture. It undertakes several critical functions, including request routing, rate limiting, and crucially, addressing authentication concerns.

A primary task of Azure API Management within this architecture is the validation of JWT tokens, which are issued by Azure Active Directory B2C. This validation ensures that every request accessing the backend is authenticated and is from a legitimate source. Once a request is authenticated, the API Management instance appends essential headers to the forwarded request. Notably, the host key is included in these headers, providing an added layer of authentication when the request reaches the Azure Functions backend.

It is worth noting that Azure API Management's role in token verification and appending security headers is part of a larger security strategy. For a deeper explanation of the proposed system’s comprehensive approach to security, including the ['defence in depth' strategy](application-security.md#defence-in-depth-strategy), visit the [Application Security wiki page](application-security.md).

#### **5.** Application Insights Integration

Application Insights will serve as the primary tool for real-time system monitoring within the proposed architecture. Unlike basic logging tools, Application Insights can be configured to actively record every function invocation, exception, and significant system event. Its telemetry capabilities provide continuous insights into system health, performance metrics, and user activity. In addition, by exposing HTTP endpoints which perform diagnostic health checks on our API, we can routinely assess essential metrics such as database connectivity and system uptime.

#### **6. Azure Key Vault**

Azure Key Vault manages and safeguards cryptographic keys and other secrets used in the application. In the proposed system design, for instance, it will be responsible for securely storing the Azure Function's host key. Azure API Management can then synchronise this key with its 'Named Values' store and append it as a header when forwarding incoming requests to the backend endpoints exposed by the Function App.

#### **7.** Blob Storage

Azure Blob Storage is included in the proposed architecture \[and required by Azure] to 'back' our Azure Function App. In addition, a separate resource group and storage account will be created to store Terraform state files for the solution. Remotely stored in a separate blob storage container, this approach facilitates collaborative infrastructure development and management, which would be ideal for distributed development teams.\
\
Both storage accounts will be configured to use Locally Redundant Storage (LRS) to strike a balance between cost-effectiveness and data durability given the intention of this application is to serve as a demonstration piece only. In scenarios demanding higher data durability across multiple regions, enterprise solutions may opt for higher redundancy levels.

#### **8.** GitHub Integration and Continuous Deployment

Central to our development and deployment strategy is GitHub. Serving as our primary code repository, it houses the application code, infrastructure scripts and necessary documentation for the proposed solution. GitHub Actions will be employed to automate critical processes in the development pipeline. Before any deployment to production, mandatory quality gates will ensure that unit and integration tests pass, static code analysis is performed, and code quality checks are met. This automation process will ensure that the deployment process is efficient, error-free, and repeatable; allowing for the successful continuous delivery of our application.

### Additional Concerns

* **Rate Limiting**: Whilst briefly touched upon above in the [Azure API Management section](system-architecture.md#4.-azure-api-management), rate limiting will ensure that the API doesn't get overwhelmed by too many requests within a short time period. As a demo application and personal project, this is especially crucial for our consumption-based plan to avoid excessive Azure costs should it exceed 'free tier' limits.
* **Terraform and Shell Scripting**: While infrastructure concerns will primarily be managed using Terraform, the initial resources required for storing the Terraform state files will be bootstrapped using an initialisation shell script. Once these initial resources have been created successfully, Terraform will take over all subsequent infrastructure management.

### Summary

Utilising Microsoft Azure, the proposed solution is designed to efficiently handle vehicle data management tasks. The architecture employs a serverless computing model, reducing infrastructure overhead, and leverages Cosmos DB for data storage, ensuring fast and efficient data access. Identity services are incorporated to manage user access and permissions, safeguarding data integrity. Additionally, integrated monitoring tools offer a granular view of system operations, enabling timely interventions where needed and ensuring a consistent level of performance. The intended design not only prioritises functionality but also emphasises security and operational transparency.

\
