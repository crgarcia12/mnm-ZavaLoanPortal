# Configuration & Externalized Settings Inventory

Configuration is concentrated in ASP.NET `web.config` plus project and container build files, with app settings used for external endpoint and connection string configuration.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| web.config | ASP.NET runtime config | `/web.config` | Connection strings, app settings, auth, authorization |
| ZavaLoanPortal.csproj | Build config | `/ZavaLoanPortal.csproj` | Target framework and build output settings |
| Dockerfile | Container/runtime config | `/Dockerfile` | Mono runtime image and xsp4 hosting command |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | `Configuration=Debug` | Development build output to `bin\` | MSBuild default CSharp targets |
| Release | `Configuration=Release` | Release build output to `bin\` | MSBuild default CSharp targets |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Default | ASP.NET runtime startup | web.config | Forms auth mode, app settings, DB connection |

## Properties Inventory

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| connectionStrings:ZavaBankDb | `Server=sqlserver,1433;Database=ZavaBankDB;...` | Default | web.config |
| appSettings:AuthGatewayLoginUrl | `http://localhost/auth/Login.aspx` | Default | web.config |
| appSettings:AuthGatewayLogoutUrl | `http://localhost/auth/Logout.aspx` | Default | web.config |
| appSettings:LoanOriginationApiUrl | `http://zava-loan-origination-api:8080/api/loanapplications` | Default | web.config |
| forms:loginUrl | `~/Login.aspx` | Default | web.config |
| forms:timeout | `30` | Default | web.config |
| compilation:debug | `true` | Default | web.config |
| compilation:targetFramework | `4.8` | Default | web.config |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| ZavaLoanPortal (xsp4) | `xsp4 --port 8080 --address 0.0.0.0 --nonstop` | Not specified | Not specified |

## Startup Dependency Chain

1. ZavaLoanPortal starts on xsp4 runtime.
2. Page workflows depend on SQL Server availability through `ZavaBankDb` connection.
3. Authentication and logout redirects depend on ZavaAuthGateway endpoints being reachable.
4. Loan submission workflow depends on Loan Origination API URL being reachable.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| connectionStrings:ZavaBankDb password | Database credential | web.config `[MASKED]` |
| machineKey validationKey | Crypto key material | web.config `[MASKED]` |
| machineKey decryptionKey | Crypto key material | web.config `[MASKED]` |

### Secrets Provisioning Workflow

Sensitive values are embedded directly in `web.config` and loaded by ASP.NET configuration providers at runtime. No external secret manager, managed identity, or dynamic secret injection workflow is declared in this repository.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|

No feature flags detected.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET Framework target | 4.8 | ZavaLoanPortal.csproj |
| ASP.NET Web Forms runtime | .NET Framework 4.8 | web.config |
| Mono base image | 6.12 | Dockerfile |
| xsp4 host | mono-xsp4 package | Dockerfile |
| .NET SDK in environment | 10.0.300 | local CLI output |
