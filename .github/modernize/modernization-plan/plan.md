# Modernization Plan: modernization-plan

**Project**: ZavaLoanPortal

---

## Technical Framework

- **Language**: C# / .NET Framework 4.8
- **Framework**: ASP.NET Web Forms
- **Build Tool**: MSBuild
- **Database**: SQL Server (connection string in web.config)
- **Key Dependencies**: System.Web, System.Data.SqlClient

---

## Overview

This migration modernizes ZavaLoanPortal for Azure hosting and managed cloud services. The application currently relies on legacy .NET Framework hosting assumptions, hardcoded endpoint configuration, and SQL authentication in configuration. The new architecture will:

- Upgrade the runtime baseline to a modern .NET LTS target for long-term support
- Migrate data-access configuration toward Azure-native SQL authentication patterns
- Externalize application configuration and deploy the app to Azure Container Apps

The migration follows a phased approach: runtime baseline upgrade, service integration and configuration migration, security remediation, then Azure deployment.

---

## Migration Impact Summary

| Application    | Original Service            | New Azure Service       | Authentication    | Comments |
|----------------|-----------------------------|-------------------------|-------------------|----------|
| ZavaLoanPortal | SQL Server connection string| Azure SQL Database      | Managed Identity  | Replace SQL auth from web.config |
| ZavaLoanPortal | Local app settings/URLs     | Azure App Configuration | Managed Identity  | Remove hardcoded service URLs |
| ZavaLoanPortal | Current container host      | Azure Container Apps    | Managed Identity  | Deploy modernized app to Azure |

---

## Planned Tasks

1. Upgrade .NET to latest LTS (`net10.0`) to establish a supported modernization baseline.
2. Migrate SQL connectivity to Azure SQL Database with managed identity authentication.
3. Move non-secret app settings to Azure App Configuration for cloud-native config management.
4. Run CVE scanning and dependency remediation before deployment.
5. Deploy the application to Azure Container Apps using Azure CLI-based deployment flow.

## Security Compliance

**Description**: Scan all project dependencies for known CVEs and remediate any identified vulnerabilities to ensure the application is secure before deployment.

**Requirements**:
Upgrade vulnerable dependencies to the minimum patched version. If a CVE fix requires a major version upgrade, document the affected dependency, the current version, the upgraded major version, and the breaking change risk. Verify that the project builds and all tests pass after remediation.
