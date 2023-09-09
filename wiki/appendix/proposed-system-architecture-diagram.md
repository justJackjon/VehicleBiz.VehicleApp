---
description: >-
  This resource contains the latest record of the visual representation of the
  diagram in addition to the underlying mermaid source code used to create it.
---

# Proposed System Architecture Diagram

## Appendix 1: Azure System Architecture Diagram Using Mermaid

### Visual Representation

<figure><img src="https://mermaid.ink/img/pako:eNp9lFFP2zAQx7-K5UnTpoE22GAlD5McO6EVVKuari_rHkzqFovEzpwEwYDvvrPjhLqDPSV3-d0_d-c7P-BcrwWO8Nbw6hpdzlcKobq96swVJn9aI9C7iWqEUbx4v8IWQIikP_uPaavyRmpVr_CvKIqcc_BRXWjThVAWQwzVdalrxOKO7kwW73BkYqWrqpA5txpoomq5vW56_arqHTtBcQZBcaGvUNZow7eig63HO3bgi-WQ_IW4R0veFk3Hg-msARZqvdeR5K5rBcqEuZW5qPuWnI9B9Vw24_YKxF4O9u3sNYZ2ZjOI9YJoZqTKZcWLLifvHry7rZpNpkMpYKApV1BqKZSvxwK7PGHPOBzQrUBMGpFDh-5RfEx9EGH_qZ8W0sl7RWoF4c8UDrsthQlLJxQdHn57XOHxYjFzGc7F71bUEP_osncQPD02F42RAtIa67qxh2O5i-Ue5cS8EPoQsCR1bOpJOv_B0PdKGO4n9NGOYZAZaZtrKMgPm9MgzBGEeWShb0T3hQbqU60ktE6qLXqLLvV2C28Om1jsfOyxbiZcwyEHRCcfKbNYNguwhTCGb7QpYYR5I56JbPY6EWcB8dqvfFueW-hKQkth5ObfyvOC1zUTG_TCLqONLIroDUnYaXoUwMEqeyz9yuhpGmru7a8nR8fJJ0ICcn95e82jsy-jkAzW1mNxPEppEmAvblL__xN6lJyFmfbL45FkxEiyV4xfFU-wk_Qk-YwPMCxCyeUa7tUHy68wzFgJBxbB65qbG7s-T8DxttHZvcpx1JhWHOC2WsPBMslh20ocbXhRD95kbYdtcApnTrvb213iT38BhX7kCA?type=png" alt=""><figcaption><p>Vehicle Data Management System Architecture Diagram</p></figcaption></figure>

### Mermaid Source Code

```mermaid
graph LR
  subgraph "Azure (Internal)"
    AF["Azure Functions"]:::AzureFunctionsColor
    CDB["Cosmos DB"]:::CosmosDBColor
    AI["Application Insights"]:::AppInsightsColor
    BS["Blob Storage"]:::BlobStorageColor
    KV["Azure Key Vault"]:::KeyVaultColor
  end
  subgraph "External Services"
    GH["GitHub"]
  end
  subgraph "Azure (External)"
    SP["Service Principal"]:::ServicePrincipalColor
    APIM["Azure API Management"]:::APIMColor
    AAD["Azure Active Directory B2C"]:::AADColor
  end
  subgraph "Client"
    AC["API Consumer"]
  end
  AC -->|"HTTP API Request"| APIM
  APIM -->|"Retrieve Host Key"| KV
  APIM -->|"HTTP Request + Host Key"| AF
  AF -->|"CRUD Operations"| CDB
  AC -->|"Authentication"| AAD
  AAD -->|"Token"| AC
  AF -->|"Monitoring & Logging"| AI
  GH -->|"GitHub Actions CI/CD"| SP
  GH -->|"Terraform State"| SP
  SP -->|"Terraform State"| BS
  SP -->|"GitHub Actions CI/CD"| AF
  APIM -->|"Token Verification"| AAD
  classDef AzureFunctionsColor fill:#AED6F1
  classDef CosmosDBColor fill:#F7DC6F
  classDef AppInsightsColor fill:#82E0AA
  classDef BlobStorageColor fill:#F1948A
  classDef KeyVaultColor fill:#BB8FCE
  classDef ServicePrincipalColor fill:#85C1E9
  classDef APIMColor fill:#E8DAEF
  classDef AADColor fill:#D5F5E3
```
