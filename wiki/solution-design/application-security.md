# Application Security

## Comprehensive Security Framework for the Proposed Vehicle Data Management API

### **Introduction**

This document details the security measures underpinning the application. Utilising a "defence in depth" methodology, multiple security layers are intertwined to ensure the comprehensive protection of the solution.

### Simplified Network Request Diagram

<figure><img src="https://mermaid.ink/img/pako:eNp9k1Fv2yAQx78KYtK0aa20deoU-aESMbZqpZGqLMrLvAdmnx00DB6GaVnb717AxIvTak_mjt_9-fs4HnClasAJbjXr9-huU0qEBvtjDEtM_loN6F0hDWjJxPsSewAhkn87buZWVoYrOZT4e5IkITnlUiWUjiWFL-l7wSvm91AhB97uzbGu74-Jk6LVbjpnBQe0Y1aYkXdhiCYYZP26-ezPufn7Yj3JugCtmWQtdCCjtgdOnRP6D3f_9RsQ5Roqo_QBLa_SWETof7ykggf5qJh6QXdy6npkO9BO4qSOpOjy8uaxxLfb7X1wuIFfFgZX_xjcB8h9I7YBozk4W7dqML5RnlvtzqggFoXQhxlL8tmxxJq9cxsvKgCEBoLQiGzVTxh30rCRx_xaSe76wmWL3qI71bZuFbDizE0QQDvQvHl5TiXYMFBo0CvThBouRPKGZPRL_mkOn41QJBdX2UdCZuRseCK2XC7yNJsLHucgItmCkiyfI_HWI0Gv8-vsM77A7k47xmv3sh48X2LX0Q5KnLhlDc04x6V8ciizRn09yAonRlu4wLavmQHKmZudDicNE8OUzWrf3SkJIVyPTzi85KdnQ68_Ug?type=png" alt=""><figcaption><p>A subsection of the proposed system architecture diagram focusing on network requests initiated by the user.</p></figcaption></figure>

The full proposed system architecture diagram is featured on the [Proposed System Architecture wiki page](system-architecture.md).

### **Authentication & Authorisation**

#### **Azure Active Directory (AAD B2C)**:

Azure Active Directory (AAD B2C) is the primary mechanism for authenticating users, utilising JWT tokens to assert user identity. Upon successful authentication, the system uses token claims to implement role-based access control, granting users appropriate permissions based on their roles. The [user stories](../functional-requirements/user-stories.md) provided define distinct roles, namely:

* **Car Buyer**: Primarily interacts with vehicle search functionalities.
* **Sales Representative**: Involved in editing, viewing, and deleting vehicle details.
* **Inventory Manager**: Focused on adding new vehicles to the system's inventory.

API Management is responsible for validating these tokens utilising a custom API level inbound policy. This inbound policy validates inbound requests before forwarding them onto the backend service endpoints - one of multiple layers of defence which is discussed further in the [Defence in Depth Strategy](application-security.md#defence-in-depth-strategy) section below. In terms of scalability, AAD B2C is able to handle millions of identities, ensuring the system can scale to accommodate an increasing user base without compromising on authentication speed or security.

### **Defence in Depth Strategy**

In addition to the primary method of [Authentication & Authorisation](application-security.md#authentication-and-authorisation) outlined above, the following strategies will be employed in order to utilise a 'defence in depth' approach:

* **Network Restriction:**\
  The Function App is specifically network restricted to accept requests solely from the Azure API Management instance. This serves as a primary line of defence against potential external threats. While higher pricing tiers support VNet integration for further isolation, this capability is not available in the current consumption-based plan configuration but should be a point for potential consideration.
* **Function App Key Authentication:**\
  Azure Key Vault securely stores the function app's host-level key and other application secrets, ensuring that they are managed securely. API Management then retrieves this key from Azure Key Vault and synchronises it with its internal 'Named Value' store, appending it to forwarded requests as a header value. This additional layer assists in ensuring only network requests authenticated by our API Management instance are able to access the backend endpoints exposed by the Function App.

### Protection against Supply Chain Attacks

This section is covered in the [Maintenance](operational-considerations.md#maintenance) section of the [Operational Considerations wiki page](operational-considerations.md).

### **Data Protection**

In terms of data protection, the system uses TLS for secure data transfer between the client and server. Azure Cosmos DB plays a pivotal role in encrypting data at rest, ensuring all data types, from backups to logs, are encrypted using Azure-managed keys. Lastly, Azure Cosmos DB's Data Plane RBAC defines specific user roles, managing and restricting the operations users can perform on the system's data.

### **Compliance**

**Notes on GDPR Alignment**\
With an emphasis on data minimisation, the system ensures that only essential data related to application functionality is stored. In alignment with the GDPR's guidelines, the architecture from its very conception has been designed with a focus on data protection. However, given that this is a demo application, for simplicity, not all GDPR-aligned features have been incorporated.

### **Audit & Monitoring**

**Application Insights**\
Logging mechanisms within Application Insights capture essential data, including function invocations and exceptions, promoting traceability and action accountability. Furthermore, telemetry features provide a detailed view of system health and operations.

[**Auditing Filter**](../code-examples/example-auditing-request-filters.md)\
In the context of Azure Functions, it is proposed that each function's invocation is meticulously logged. These logs detail the user, action taken, the timestamp of the action, and other pertinent information, reinforcing the traceability and accountability of all performed actions.

### **Summary**

The proposed design decisions embody a multi-faceted security approach. From robust authentication and authorisation to meticulous auditing, each aspect is engineered to fortify the application against potential threats, while also ensuring compliance with pivotal regulations like the GDPR.
