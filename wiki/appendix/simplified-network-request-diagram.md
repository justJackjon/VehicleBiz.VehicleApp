---
description: >-
  This resource contains the latest record of the visual representation of the
  diagram in addition to the underlying mermaid source code used to create it.
---

# Simplified Network Request Diagram

## Appendix 2: Simplified Network Request Diagram Using Mermaid

### Visual Representation

<figure><img src="https://mermaid.ink/img/pako:eNp9k1Fv2yAQx78KYtK0aa20deoU-aESMbZqpZGqLMrLvAdmnx00DB6GaVnb717AxIvTak_mjt_9-fs4HnClasAJbjXr9-huU0qEBvtjDEtM_loN6F0hDWjJxPsSewAhkn87buZWVoYrOZT4e5IkITnlUiWUjiWFL-l7wSvm91AhB97uzbGu74-Jk6LVbjpnBQe0Y1aYkXdhiCYYZP26-ezPufn7Yj3JugCtmWQtdCCjtgdOnRP6D3f_9RsQ5Roqo_QBLa_SWETof7ykggf5qJh6QXdy6npkO9BO4qSOpOjy8uaxxLfb7X1wuIFfFgZX_xjcB8h9I7YBozk4W7dqML5RnlvtzqggFoXQhxlL8tmxxJq9cxsvKgCEBoLQiGzVTxh30rCRx_xaSe76wmWL3qI71bZuFbDizE0QQDvQvHl5TiXYMFBo0CvThBouRPKGZPRL_mkOn41QJBdX2UdCZuRseCK2XC7yNJsLHucgItmCkiyfI_HWI0Gv8-vsM77A7k47xmv3sh48X2LX0Q5KnLhlDc04x6V8ciizRn09yAonRlu4wLavmQHKmZudDicNE8OUzWrf3SkJIVyPTzi85KdnQ68_Ug?type=png" alt=""><figcaption><p>A subsection of the proposed system architecture diagram focusing on network requests initiated by the user.</p></figcaption></figure>

The full proposed system architecture diagram is featured on the [Proposed System Architecture Diagram wiki page](../solution-design/system-architecture.md)[.](proposed-system-architecture-diagram.md)

### Mermaid Source Code

```mermaid
graph LR
  subgraph "Azure (Internal)"
    AF["Azure Functions"]:::AzureFunctionsColor
    AI["Application Insights"]:::AppInsightsColor
    KV["Azure Key Vault"]:::KeyVaultColor
  end
  subgraph "Azure (External)"
    APIM["Azure API Management"]:::APIMColor
    AAD["Azure Active Directory B2C"]:::AADColor
  end
  subgraph "Client"
    AC["API Consumer"]
  end
  AC -->|"HTTP API Request"| APIM
  APIM -->|"Retrieve Host Key"| KV
  APIM -->|"HTTP Request + Host Key"| AF
  AC -->|"Authentication"| AAD
  AAD -->|"Token"| AC
  AF -->|"Monitoring & Logging"| AI
  APIM -->|"Token Verification"| AAD
  classDef AzureFunctionsColor fill:#AED6F1
  classDef AppInsightsColor fill:#82E0AA
  classDef KeyVaultColor fill:#BB8FCE
  classDef APIMColor fill:#E8DAEF
  classDef AADColor fill:#D5F5E3
```
