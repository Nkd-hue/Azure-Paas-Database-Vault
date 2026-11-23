# Azure-Paas-Database-Vault

# Modernizing to Azure PaaS and Securing Secrets with Azure Key Vault

## Project Overview

This project demonstrates the modernization of an existing Azure infrastructure by replacing a manually managed database virtual machine with Azure SQL Database (PaaS). It also implements secure credential management using Azure Key Vault and Managed Identity.

The project follows a cloud modernization approach that reduces infrastructure management responsibilities while improving security and resource monitoring.

## Project Objectives

- Decommission the existing database VM, `vm-db-01`.
- Deploy Azure SQL Database as a Platform as a Service (PaaS) solution.
- Deploy Azure Key Vault to securely store database credentials.
- Enable a system-assigned Managed Identity on the existing web server VM.
- Configure Azure role-based access control (RBAC) to grant the VM access to Key Vault secrets.
- Use Azure Monitor to validate database performance metrics.

## Technologies Used

- Microsoft Azure
- Azure Virtual Machines
- Azure SQL Database
- Azure Key Vault
- Azure Managed Identity
- Azure RBAC
- Azure Monitor
- Microsoft Azure Portal

## Architecture Overview

The architecture replaces the self-managed database VM with Azure SQL Database while retaining the existing web server.

The web server uses its system-assigned Managed Identity to authenticate to Azure Key Vault. The identity receives the Key Vault Secrets User role, allowing it to retrieve secret values without requiring credentials to be stored in application code.

The Azure SQL Database is monitored through Azure Monitor.

**Architecture Components**

| Component | Purpose |
|---|---|
| `vm-web-01` | Existing web server virtual machine |
| `vm-db-01` | Previous database VM, decommissioned |
| Azure SQL Database | Managed relational database |
| Azure Key Vault | Secure storage for database credentials |
| Managed Identity | Passwordless authentication between Azure resources |
| Azure RBAC | Controls access to Key Vault secrets |
| Azure Monitor | Monitors database performance metrics |

## Implementation

### Phase 1: Decommission the Existing Database VM

The first phase involved removing the existing database virtual machine from the Lab 02 resource group.

The following resources associated with `vm-db-01` were selected for deletion:

- Database virtual machine (`vm-db-01`)
- Associated operating system disk
- Associated network interface

The existing web server, `vm-web-01`, was retained.

**Screenshot Evidence**
<img width="2815" height="1601" alt="Screenshot 2026-11-22 at 2 30 50 PM" src="https://github.com/user-attachments/assets/19fd4aed-fc5d-4ee8-ae2d-c1601293a0bf" />

*Evidence: Azure portal showing the removal of the database VM and its associated resources.*

---

### Phase 2: Deploy Azure SQL Database (PaaS)

Azure SQL Database was selected to replace the manually managed database VM.

The deployment involved creating a logical SQL server and provisioning a database named `sql-app`.

Key configuration settings included:

- SQL server name: `sql-server- Nadia`
- Database name: `sql-app`
- Workload environment: Development
- Pricing tier: Basic DTU
- Connectivity: Public endpoint 
- Minimum TLS version: TLS 1.2
- Backup storage redundancy: Locally redundant storage (LRS)

**Screenshot Evidence**

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 2 49 22 PM" src="https://github.com/user-attachments/assets/e2b7fbfb-0ad8-4259-a724-4a7a25a2dabd" />

*Evidence: Azure portal showing the SQL database deployment and database status.*

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 2 52 23 PM" src="https://github.com/user-attachments/assets/cc46847e-3903-43e0-8a91-8e14e06bc175" />

*Evidence: SQL logical server configuration and connectivity settings.*

---

### Phase 3: Deploy Azure Key Vault

Azure Key Vault was introduced to provide centralized and secure storage for sensitive database credentials.

A Key Vault was configured with the following settings:

- Key Vault name: `kv-lab03- Nadia`
- Pricing tier: Standard
- Permission model: Azure role-based access control (RBAC)
- Region: West US Central (This is where my SQL-Database was deployed)

The Key Vault Administrator role was assigned to the administrator account to allow management of the vault and its secrets.

**Screenshot Evidence**

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 2 58 46 PM" src="https://github.com/user-attachments/assets/87801eb8-aa95-4185-84d5-23a3bf08cd6a" />

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 2 59 48 PM" src="https://github.com/user-attachments/assets/69dc15ab-70a5-4994-81f0-9bd7fff4fa08" />

*Evidence: Azure portal showing the deployed Key Vault and its configuration.*

<img width="1407" height="761" alt="Screenshot 2026-11-22 at 11 05 21 PM" src="https://github.com/user-attachments/assets/d02f5b14-9631-4420-a30b-8c58a3801628" />

*Evidence: Azure RBAC showing the Key Vault Administrator role assignment.*

---

### Phase 4: Store the SQL Password as a Key Vault Secret

A secret named `SqlAdminPassword` was created in Azure Key Vault.

This approach keeps sensitive credentials out of application code and provides a centralized location for managing secrets.

The secret was configured with the following properties:

- Secret name: `SqlAdmin`
- Upload option: Manual
- Status: Enabled

**Security Note:** The actual password must never be included in GitHub, README files, screenshots, or other publicly accessible documentation.

**Screenshot Evidence**
<img width="1408" height="881" alt="Screenshot 2026-11-22 at 10 52 15 PM" src="https://github.com/user-attachments/assets/55d6da33-9114-4973-b5fa-a4ec5f509b1f" />


*Evidence: Azure Key Vault showing the SqlAdmin secret and its enabled status. The secret value must remain hidden.*

---

### Phase 5: Enable Managed Identity and Configure RBAC

A system-assigned Managed Identity was enabled on the existing web server virtual machine, `vm-web-01`.

This identity allows the VM to authenticate to supported Azure services without storing a username or password.

The following security configuration was implemented:

1. Enabled the system-assigned Managed Identity on `vm-web-01`.
2. Verified that Azure generated an Object (principal) ID.
3. Navigated to the Key Vault Access Control (IAM) settings.
4. Assigned the **Key Vault Secrets User** role to the VM's Managed Identity.
5. Scoped the role assignment to the Key Vault resource.

The Key Vault Secrets User role provides read access to secret values, following the principle of least privilege.

**Screenshot Evidence**

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 11 30 02 PM" src="https://github.com/user-attachments/assets/26dcaa2e-d465-4248-b644-ef797f137bfe" />

*Evidence: Azure virtual machine showing the enabled system-assigned Managed Identity.*

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 11 40 16 PM" src="https://github.com/user-attachments/assets/26b30c41-729d-4e69-8f8f-1a390ea137b6" />

*Evidence: Azure Key Vault IAM showing the Key Vault Secrets User role assigned to vm-web-01.*

---

### Phase 6: Validate Database Performance Using Azure Monitor

Azure Monitor was used to validate that the deployed Azure SQL Database was producing performance metrics.

The monitoring configuration depended on the database pricing tier:

- **Basic DTU tier:** DTU Percentage metric with Max aggregation.
- **Free Serverless tier:** CPU Percentage metric.

A metrics chart was reviewed to confirm that the database was reporting monitoring data.

**Screenshot Evidence**

<img width="1408" height="881" alt="Screenshot 2026-11-22 at 11 52 05 PM" src="https://github.com/user-attachments/assets/fa5c4018-1477-4098-89e6-a8adc9407a85" />

*Evidence: Azure Monitor displaying the selected database performance metric.*

---

## Security Controls

The project demonstrates several important cloud security practices.

| Security Control | Implementation |
|---|---|
| Secrets Management | Azure Key Vault stores the SQL administrator password |
| Identity Management | System-assigned Managed Identity on the web server |
| Access Control | Azure RBAC controls access to Key Vault |
| Least Privilege | Key Vault Secrets User provides read-only secret access |
| Encryption in Transit | TLS 1.2 configured for the SQL server |
| Monitoring | Azure Monitor tracks database performance metrics |

**Network Security Consideration**

The lab uses a public endpoint and network access settings for learning purposes. A production deployment should evaluate restricted network access, private endpoints, and other appropriate network security controls.

## Key Concepts Demonstrated

- Infrastructure as a Service (IaaS) versus Platform as a Service (PaaS)
- Azure SQL Database deployment
- Cloud infrastructure modernization
- Azure Key Vault secret management
- System-assigned Managed Identity
- Azure role-based access control
- Least-privilege access management
- Azure Monitor and observability

## Project Outcomes

This project demonstrates the process of modernizing a cloud database infrastructure while introducing secure identity-based access to sensitive credentials.

It provides practical experience with Azure PaaS services, secrets management, Managed Identity, RBAC, and cloud resource monitoring.


## Author

**Nadia Kroduah**

Cloud and Network Engineering | Azure Infrastructure | Identity and Access Management
