# Configuration & Externalized Settings Inventory

The project uses a compact configuration model centered on `web.config`, with connection strings, external service URLs, and ASP.NET authentication settings.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| web.config | XML application config | `/web.config` | Primary runtime config for connection strings, app settings, and system.web auth |
| Dockerfile | Container runtime config | `/Dockerfile` | Declares IIS/.NET Framework runtime image and hosting behavior |
| csproj properties | Build config | `/ZavaLoanPortal.csproj` | Defines target framework and Debug/Release build outputs |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | Default in local builds when unspecified | Local debugging build output | MSBuild built-in targets |
| Release | Explicit `Configuration=Release` | Production-oriented build output | MSBuild built-in targets |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Default | IIS/ASP.NET app start | `web.config` | Forms auth enabled, customErrors off, SQL and service URLs |

## Properties Inventory

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| connectionStrings:ZavaBankDb | SQL Server connection string | Default | web.config |
| appSettings:AuthGatewayLoginUrl | `http://localhost/auth/Login.aspx` | Default | web.config |
| appSettings:AuthGatewayLogoutUrl | `http://localhost/auth/Logout.aspx` | Default | web.config |
| appSettings:LoanOriginationApiUrl | `http://zava-loan-origination-api:8080/api/loanapplications` | Default | web.config |
| system.web/forms timeout | `30` | Default | web.config |
| system.web/forms name | `.ZAVAAUTH` | Default | web.config |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| ZavaLoanPortal | No explicit startup flags detected in repository | Not specified | Not specified |

## Startup Dependency Chain

1. IIS/.NET Framework runtime starts ZavaLoanPortal.
2. ZavaLoanPortal requires SQL Server availability for page data queries.
3. Authentication and logout user journeys depend on external auth gateway URL availability.
4. Loan submission path depends on external loan origination API availability.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `connectionStrings:ZavaBankDb` password | DB credential | web.config (`[MASKED]`) |
| `system.web/machineKey validationKey` | Crypto key | web.config (`[MASKED]`) |
| `system.web/machineKey decryptionKey` | Crypto key | web.config (`[MASKED]`) |

### Secrets Provisioning Workflow

Secrets appear to be statically provisioned in `web.config` and loaded by ASP.NET configuration at runtime. No external secret manager integration or managed identity flow is defined in repository configuration files.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| None detected | N/A | N/A |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET Framework target | 4.8 | ZavaLoanPortal.csproj |
| ASP.NET runtime target | 4.8 | web.config system.web |
| MSBuild project schema | ToolsVersion 4.0 | ZavaLoanPortal.csproj |
| dotnet-appcat (assessment tool) | 1.0.1127 | tool install output |
