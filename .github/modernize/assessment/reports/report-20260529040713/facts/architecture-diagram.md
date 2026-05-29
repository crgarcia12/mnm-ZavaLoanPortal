# Architecture Diagram

This .NET Framework Web Forms application provides a loan portal UI that reads/writes SQL Server data and calls external authentication and loan-origination services.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - ASP.NET Web Forms 4.8"]
        Pages["Web Forms Pages"]
        Auth["Forms Authentication"]
        Logic["Code-behind Business Logic"]
        HttpClient["HttpWebRequest API Client"]
    end
    subgraph Data["Data Layer"]
        ADO["ADO.NET SqlClient"]
        DB[("SQL Server ZavaBankDB")]
    end
    subgraph External["External Services"]
        AuthGateway["Zava Auth Gateway"]
        LoanApi["Loan Origination API"]
    end

    Browser -->|"HTTP requests"| Pages
    Pages --> Auth
    Pages -->|"page events"| Logic
    Logic -->|"SQL commands"| ADO
    ADO -->|"T-SQL"| DB
    Logic -->|"redirect login/logout"| AuthGateway
    HttpClient -->|"POST XML"| LoanApi
    Logic --> HttpClient
```

### Technology Stack Summary

| Layer | Technology | Version | Purpose |
|---|---|---|---|
| Presentation | ASP.NET Web Forms | .NET Framework 4.8 | Server-rendered loan portal UI |
| Security | Forms Authentication | .NET Framework 4.8 | Session/cookie-based portal authentication |
| Business | C# code-behind classes | .NET Framework 4.8 | Loan submission workflow and page orchestration |
| Data Access | ADO.NET SqlClient | System.Data.SqlClient | SQL queries and inserts |
| Data Storage | SQL Server | Not specified | Stores loan products and loan applications |

### Data Storage & External Services

The portal stores application and product data in a SQL Server database (`ZavaBankDb` connection string). It also depends on an external authentication gateway for login/logout redirects and a loan-origination API for POSTing loan applications.

### Key Architectural Decisions

- Uses ASP.NET Web Forms page lifecycle and server controls instead of MVC/API controllers.
- Uses direct ADO.NET SQL commands rather than a repository/ORM abstraction.
- Integrates external systems with configuration-driven URLs in `web.config`.

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation
        DefaultPage["Default.aspx Page"]
        LoginPage["Login.aspx Page"]
        LogoutPage["Logout.aspx Page"]
        MasterPage["Site.Master"]
    end
    subgraph Business["Business Logic"]
        DefaultCode["Default.aspx.cs"]
        LoginCode["Login.aspx.cs"]
        LogoutCode["Logout.aspx.cs"]
    end
    subgraph DataAccess["Data Access"]
        SqlConn["SqlConnection"]
        SqlCmd["SqlCommand"]
    end
    subgraph Infra["Infrastructure"]
        FormsAuth["Forms Authentication"]
        AppSettings["web.config appSettings"]
    end

    DefaultPage -->|"events"| DefaultCode
    LoginPage -->|"events"| LoginCode
    LogoutPage -->|"events"| LogoutCode
    DefaultCode -->|"queries/inserts"| SqlConn
    SqlConn --> SqlCmd
    LoginCode -->|"login URL"| AppSettings
    LogoutCode -->|"logout URL"| AppSettings
    DefaultCode -->|"LoanOriginationApiUrl"| AppSettings
    FormsAuth -.->|"protects"| Presentation
    MasterPage -.->|"shared layout"| Presentation
```

### Component Inventory

| Component | Layer | Type | Responsibility |
|---|---|---|---|
| Default.aspx / Default.aspx.cs | Presentation + Business | Web Forms page + code-behind | Loan application wizard, product/history data binding, loan submission |
| Login.aspx / Login.aspx.cs | Presentation + Business | Web Forms page + code-behind | Redirects users to external auth gateway login |
| Logout.aspx / Logout.aspx.cs | Presentation + Business | Web Forms page + code-behind | Signs out and redirects to external logout endpoint |
| Site.Master | Presentation | Master page | Shared UI shell for portal pages |
| SqlConnection / SqlCommand | Data Access | ADO.NET components | Executes SQL reads/writes to SQL Server |
| Forms authentication config | Infrastructure | ASP.NET auth configuration | Enforces authenticated access except login/logout pages |
