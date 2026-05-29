# Architecture Diagram

This repository is a .NET Framework Web Forms loan portal with a server-rendered UI, direct SQL Server access, and outbound HTTP integration to an external loan origination API.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - ASP.NET Web Forms .NET Framework 4.8"]
        Pages["ASPX Pages and Wizard UI"]
        Auth["Forms Authentication"]
        Logic["Page Code Behind Business Logic"]
    end
    subgraph Data["Data Layer"]
        ADO["ADO.NET SqlClient"]
        SQL[("SQL Server ZavaBankDB")]
    end
    subgraph External["External Services"]
        AuthGateway["ZavaAuthGateway"]
        LoanApi["Loan Origination API"]
    end

    Browser -->|"HTTPS requests"| Pages
    Pages -->|"auth cookie check"| Auth
    Pages -->|"delegates actions"| Logic
    Logic -->|"SQL queries and inserts"| ADO
    ADO -->|"T-SQL"| SQL
    Logic -->|"redirect for sign in and sign out"| AuthGateway
    Logic -->|"POST loan XML"| LoanApi
```

### Technology Stack Summary

| Layer | Technology | Version | Purpose |
|---|---|---|---|
| Presentation | ASP.NET Web Forms | .NET Framework 4.8 | Server rendered pages and wizard workflow |
| Authentication | Forms Authentication | .NET Framework 4.8 | Auth cookie based access control |
| Business Logic | C# code behind pages | .NET Framework 4.8 | Handles workflow steps and submission actions |
| Data Access | ADO.NET SqlClient | System.Data | Executes SQL reads and writes |
| Data Storage | SQL Server | Not pinned in repo | Stores loan products and loan applications |
| External Integration | HTTP WebRequest | System.Net | Sends loan applications to origination API |

### Data Storage & External Services

The application persists loan product and application records in SQL Server using a single configured connection string. It also depends on external services for authentication redirection and loan origination submission via an HTTP endpoint.

### Key Architectural Decisions

- Uses ASP.NET Web Forms page lifecycle and event handlers instead of controller based APIs.
- Uses direct SQL in code behind with ADO.NET rather than repository or ORM abstractions.
- Uses forms authentication with explicit allow rules for login and logout pages.

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation
        DefaultPage["Default.aspx and Wizard"]
        LoginPage["Login.aspx"]
        LogoutPage["Logout.aspx"]
        HistoryGrid["Loan History Grid"]
    end
    subgraph Business["Business Logic"]
        DefaultCodeBehind["Default.aspx.cs"]
        LoginCodeBehind["Login.aspx.cs"]
        LogoutCodeBehind["Logout.aspx.cs"]
    end
    subgraph DataAccess["Data Access"]
        LoanProductsQuery["LoanProducts SELECT"]
        LoanHistoryQuery["LoanApplications SELECT TOP 25"]
        InsertApplication["LoanApplications INSERT"]
    end
    subgraph Infrastructure
        FormsAuth["Forms Authentication"]
        LoanApiClient["HttpWebRequest Client"]
        Config["web.config settings"]
    end

    DefaultPage -->|"events"| DefaultCodeBehind
    LoginPage -->|"page load"| LoginCodeBehind
    LogoutPage -->|"page load"| LogoutCodeBehind
    DefaultCodeBehind -->|"bind products"| LoanProductsQuery
    DefaultCodeBehind -->|"bind history"| LoanHistoryQuery
    DefaultCodeBehind -->|"submit loan"| InsertApplication
    DefaultCodeBehind -->|"send payload"| LoanApiClient
    LoginCodeBehind -->|"auth URL lookup"| Config
    LogoutCodeBehind -->|"logout URL lookup"| Config
    DefaultCodeBehind -->|"uses"| FormsAuth
    HistoryGrid -->|"row command"| DefaultCodeBehind
```

### Component Inventory

| Component | Layer | Type | Responsibility |
|---|---|---|---|
| Default.aspx | Presentation | Web Forms Page | Hosts loan application wizard and history grid |
| Default.aspx.cs | Business Logic | Page code behind | Validates step input, saves application, calls external API |
| Login.aspx.cs | Business Logic | Page code behind | Redirects unauthenticated users to auth gateway |
| Logout.aspx.cs | Business Logic | Page code behind | Signs out local auth and redirects to auth gateway logout |
| LoanProducts query | Data Access | SQL query | Loads active loan products for selection |
| LoanApplications insert | Data Access | SQL command | Persists submitted loan applications |
| Loan API client | Infrastructure | HTTP client integration | Posts XML loan submission payload |
| Forms authentication | Infrastructure | Security middleware | Enforces authenticated access to protected pages |
