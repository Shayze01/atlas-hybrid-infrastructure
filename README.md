
# Atlas Hybrid Infrastructure

Enterprise-style hybrid infrastructure lab demonstrating Azure networking, Windows Server, Active Directory, DNS, identity and access management, domain-joined workstations, Group Policy, endpoint security, Windows event auditing, and structured infrastructure troubleshooting.

## Project Overview

Atlas Hybrid Infrastructure is a hands-on infrastructure engineering project designed to build, secure, validate, and document an enterprise-style Microsoft environment using Microsoft Azure, Windows Server, and Windows client operating systems.

The project follows a structured engineering workflow:

**Design → Build → Validate → Document → Evidence**

Each lab contains practical implementation activities, configuration validation, technical troubleshooting, and supporting evidence demonstrating the work performed.

The environment is developed progressively, with each lab extending the infrastructure established in previous stages.

### Project Objectives

- Design and deploy Microsoft Azure infrastructure.
- Configure secure virtual networking and subnet segmentation.
- Deploy and administer Windows Server virtual machines.
- Implement Active Directory Domain Services and DNS.
- Manage enterprise identities, Organizational Units, and security groups.
- Deploy and validate domain-joined Windows workstations.
- Implement centrally managed workstation security policies.
- Configure Windows Defender Firewall and controlled remote administration.
- Enforce workstation inactivity locking.
- Configure and investigate Windows security auditing.
- Develop practical PowerShell administration and troubleshooting skills.
- Maintain professional technical documentation and implementation evidence.

---

## Environment

| Component | Configuration |
|---|---|
| Cloud Platform | Microsoft Azure |
| Azure Region | West US 2 |
| Resource Group | `rg-atlas-lab` |
| Virtual Network | `vnet-atlas-lab-us` |
| Address Space | `10.20.0.0/16` |
| Server Subnet | `snet-servers` — `10.20.10.0/24` |
| Client Subnet | `snet-clients` — `10.20.20.0/24` |
| Azure Bastion Subnet | `AzureBastionSubnet` — `10.20.30.0/26` |
| Domain Controller | `DC01` |
| DC01 Private IP | `10.20.10.10` |
| Server Operating System | Windows Server 2022 Datacenter: Azure Edition |
| Client Workstation | `CL01` |
| CL01 Private IP | `10.20.20.4` |
| Client Operating System | Windows 11 Pro |
| Active Directory Domain | `corp.atlas.local` |
| NetBIOS Domain | `CORP` |
| Remote Administration | Azure Bastion |
| Bastion Resource | `bas-atlas-lab-us` |

### Active Directory Structure

The environment uses a structured Organizational Unit hierarchy to support identity administration, computer management, and targeted security policy deployment.

```text
corp.atlas.local
└── Atlas
    ├── Computers
    │   ├── Servers
    │   └── Workstations
    │       └── CL01
    ├── Users
    │   ├── IT
    │   ├── Finance
    │   └── HR
    ├── Groups
    └── Service Accounts
```

---

## Completed Labs

### LAB-001 — Azure Network Foundation

Designed and deployed the Azure network foundation for the Atlas environment, including the Virtual Network and dedicated server and client subnets.

**Key Skills:**
- Azure Virtual Networks
- IPv4 addressing
- Subnet design
- Azure resource deployment
- Infrastructure validation
- Troubleshooting regional resource availability

**Evidence:** [`evidence/lab-001`](evidence/lab-001)

---

### LAB-002 — Windows Server DC01 Deployment

Deployed and validated the Windows Server virtual machine that provides the server foundation for the Atlas Active Directory environment.

**Key Skills:**
- Azure Virtual Machines
- Windows Server 2022
- Azure VM networking
- Network Security Groups
- Private IP configuration
- Windows network validation
- Infrastructure troubleshooting

**Evidence:** [`evidence/lab-002`](evidence/lab-002)

---

### LAB-003 — Active Directory Domain Services & DNS

Deployed Active Directory Domain Services on `DC01`, created the `corp.atlas.local` forest, integrated DNS, and validated the health and availability of the Domain Controller.

Validation included DNS resolution, Active Directory diagnostics, and verification of SYSVOL and NETLOGON.

**Key Skills:**
- Active Directory Domain Services
- Domain Controller deployment
- Active Directory forest configuration
- Windows DNS
- DNS name resolution
- `dcdiag`
- SYSVOL and NETLOGON validation
- Windows Server administration

**Documentation:** [LAB-003 — Active Directory Domain Services & DNS](docs/LAB-003-active-directory-domain-services-dns.md)

**Evidence:** [`evidence/lab-003`](evidence/lab-003)

---

### LAB-004 — Active Directory Identity & Access Management

Implemented an enterprise-style identity and access management structure within the `corp.atlas.local` domain using Organizational Units, departmental user accounts, and Global Security Groups.

User-to-group membership was validated through Active Directory Users and Computers and independently verified using PowerShell.

**Key Skills:**
- Active Directory administration
- Organizational Unit design
- User provisioning
- Global Security Groups
- Group-based access management
- Active Directory PowerShell
- Identity and access management
- Infrastructure validation

**Documentation:** [LAB-004 — Active Directory Identity & Access Management](docs/LAB-004-active-directory-identity-access-management.md)

**Evidence:** [`evidence/lab-004`](evidence/lab-004)

---

### LAB-005 — Domain-Joined Client Infrastructure

Deployed a Windows 11 Pro workstation within the existing Azure network and integrated it into the `corp.atlas.local` Active Directory domain.

The client was configured to use the domain's DNS infrastructure, successfully joined to Active Directory, and validated for domain connectivity, authentication, and remote administration.

The workstation computer object was organised within the dedicated Workstations OU to support targeted computer management and future Group Policy deployment.

**Key Implementation Activities:**
- Windows 11 virtual machine deployment.
- Client subnet integration.
- Private IP and DNS configuration.
- Active Directory domain join.
- Domain controller discovery and connectivity testing.
- Domain user authentication.
- Active Directory computer object organisation.
- Azure Bastion remote access.
- PowerShell-based configuration validation.

**Key Skills:**
- Windows 11 administration
- Azure Virtual Machines
- Domain-joined client deployment
- Active Directory computer management
- Windows DNS client configuration
- Domain authentication
- Azure Bastion
- Windows networking
- PowerShell troubleshooting
- Infrastructure validation

**Documentation:** [LAB-005 — Domain-Joined Client Infrastructure](docs/LAB-005-domain-joined-client-infrastructure.md)

**Evidence:** [`evidence/lab-005`](evidence/lab-005)

---

### LAB-006 — Group Policy Management & Workstation Security

Implemented a centrally managed workstation security baseline using Active Directory Group Policy.

Created and linked the `ATLAS-Workstation-Security-Baseline` GPO to the Workstations OU and validated its application on the domain-joined Windows 11 client.

Configured Windows Defender Firewall, deployed a domain-profile RDP allow rule restricted to the Azure Bastion subnet, enforced automatic workstation locking after 600 seconds of inactivity, and enabled successful and failed logon auditing.

Investigated Windows Security Event IDs 4624 and 4625 to validate authentication monitoring and demonstrate security event troubleshooting.

**Key Implementation Activities:**
- Group Policy Management Console administration.
- Workstation-specific GPO creation and linking.
- Computer-level Group Policy deployment.
- Microsoft Defender Firewall profile configuration.
- RDP firewall rule restricted to `10.20.30.0/26`.
- Azure Bastion connectivity validation.
- Automatic workstation inactivity lock configuration.
- Advanced Audit Policy Configuration.
- Successful and failed logon auditing.
- Windows Security Event Viewer investigation.
- Authentication failure analysis.
- PowerShell-based security validation.

**Key Skills:**
- Windows Group Policy
- Group Policy Management Console
- Organizational Unit-based policy targeting
- Windows Defender Firewall
- Windows endpoint security
- Remote Desktop security
- Azure Bastion
- Windows Security Auditing
- Event ID 4624 investigation
- Event ID 4625 investigation
- Windows Event Viewer
- PowerShell administration
- Security troubleshooting
- Configuration validation

**Documentation:** [LAB-006 — Group Policy Management & Workstation Security](docs/LAB-006-group-policy-management-workstation-security.md)

**Evidence:** [`evidence/lab-006`](evidence/lab-006)

---

## Infrastructure Progress

The Atlas environment has progressed through six completed implementation stages.

```text
LAB-001  Azure Network Foundation
   │
   ▼
LAB-002  Windows Server DC01 Deployment
   │
   ▼
LAB-003  Active Directory Domain Services & DNS
   │
   ▼
LAB-004  Active Directory Identity & Access Management
   │
   ▼
LAB-005  Domain-Joined Client Infrastructure
   │
   ▼
LAB-006  Group Policy Management & Workstation Security
   │
   ▼
LAB-007  Windows Server File Services, NTFS Permissions & SMB Shares
         (Next Phase)
```

### Current Infrastructure Capabilities

The environment now provides:

- Azure virtual networking with dedicated server and client subnets.
- A Windows Server 2022 Active Directory Domain Controller.
- Integrated Active Directory DNS services.
- Structured Organizational Units and departmental identities.
- Global Security Groups for identity and access management.
- A Windows 11 domain-joined workstation.
- Domain user authentication and computer account management.
- Azure Bastion remote administration.
- Centralised workstation security policy management.
- Windows Defender Firewall configuration.
- RDP access restricted through a custom Bastion subnet rule.
- Automatic workstation inactivity locking.
- Successful and failed logon auditing.
- Windows Security Event investigation and troubleshooting.

These capabilities establish a foundation for subsequent file services, access control, infrastructure administration, and hybrid cloud engineering exercises.

---

## Technologies

### Cloud Infrastructure
- Microsoft Azure
- Azure Virtual Networks
- Azure Virtual Machines
- Azure Network Security Groups
- Azure Bastion
- Azure Resource Groups

### Windows Server & Identity
- Windows Server 2022
- Active Directory Domain Services
- Windows DNS
- Active Directory Users and Computers
- Active Directory Administrative Center
- Group Policy Management Console
- Organizational Units
- Active Directory Security Groups

### Windows Client & Endpoint Security
- Windows 11 Pro
- Windows Defender Firewall with Advanced Security
- Windows Group Policy
- Remote Desktop Protocol
- Windows Security Event Viewer
- Advanced Audit Policy Configuration

### Administration & Troubleshooting
- Windows PowerShell
- Active Directory PowerShell
- Windows command-line utilities
- `dcdiag`
- `nltest`
- `gpupdate`
- `gpresult`
- `auditpol`
- `Get-WinEvent`
- `Get-NetFirewallRule`
- `Get-NetFirewallProfile`

### Documentation & Version Control
- Git
- GitHub
- Markdown
- Technical documentation
- Screenshot-based implementation evidence

---

## Repository Structure

```text
atlas-hybrid-infrastructure/
├── docs/
│   ├── LAB-003-active-directory-domain-services-dns.md
│   ├── LAB-004-active-directory-identity-access-management.md
│   ├── LAB-005-domain-joined-client-infrastructure.md
│   └── LAB-006-group-policy-management-workstation-security.md
│
├── evidence/
│   ├── lab-001/
│   ├── lab-002/
│   ├── lab-003/
│   ├── lab-004/
│   ├── lab-005/
│   └── lab-006/
│
├── LICENSE
└── README.md
```

Each lab evidence directory contains screenshots supporting the relevant deployment, configuration, troubleshooting, and validation activities.

---

## Engineering Approach

Project Atlas follows a repeatable implementation methodology.

**1. Design**

Define infrastructure requirements, dependencies, network configuration, and the intended technical outcome.

**2. Build**

Deploy and configure infrastructure components using Microsoft Azure, Windows Server, Active Directory, and Windows administration tools.

**3. Validate**

Use administrative consoles, PowerShell, command-line utilities, and functional testing to verify that deployed services behave as expected.

**4. Troubleshoot**

Investigate configuration errors, connectivity issues, authentication problems, and policy processing failures.

**5. Document**

Record implementation steps, commands, configurations, observed results, and troubleshooting decisions.

**6. Capture Evidence**

Maintain supporting screenshots and validation outputs in the repository to demonstrate the work performed.

---

## Project Status

| Lab | Description | Status |
|---|---|---|
| LAB-001 | Azure Network Foundation | Completed |
| LAB-002 | Windows Server DC01 Deployment | Completed |
| LAB-003 | Active Directory Domain Services & DNS | Completed |
| LAB-004 | Active Directory Identity & Access Management | Completed |
| LAB-005 | Domain-Joined Client Infrastructure | Completed |
| LAB-006 | Group Policy Management & Workstation Security | Completed |
| LAB-007 | Windows Server File Services, NTFS Permissions & SMB Shares | Next |

**Completed Labs:** 6

**Current Phase:** Windows infrastructure administration, identity management, and workstation security.

**Next Phase:** Windows Server File Services, NTFS Permissions & SMB Shares.

---

## Next Lab — LAB-007

### Windows Server File Services, NTFS Permissions & SMB Shares

The next phase will extend the Atlas environment by introducing centrally managed Windows file services.

Planned areas include:

- Windows Server File Services configuration.
- SMB shared folder deployment.
- NTFS permission management.
- Share-level permissions.
- Active Directory group-based resource access.
- Departmental file access controls.
- Access validation using domain user accounts.
- File access troubleshooting.
- PowerShell administration and verification.
- Structured documentation and implementation evidence.

This phase will build on the existing Active Directory identities, security groups, domain-joined workstation, and Windows Server infrastructure.

---

## Project Purpose

Atlas Hybrid Infrastructure is an independently developed technical portfolio project intended to demonstrate practical skills in infrastructure engineering, Windows systems administration, Active Directory, endpoint security, and Microsoft Azure.

The project prioritises hands-on implementation, technical accuracy, troubleshooting, and verifiable documentation.

All configurations and evidence relate to the dedicated Atlas lab environment.
