# Atlas Hybrid Infrastructure

Enterprise-style hybrid infrastructure lab demonstrating Azure networking, Windows Server, Active Directory, DNS, Group Policy, identity, access control, and structured infrastructure troubleshooting.

## Project Overview

Atlas Hybrid Infrastructure is a hands-on infrastructure engineering project designed to build and document an enterprise-style Microsoft environment using Microsoft Azure and Windows Server.

The project follows a structured engineering workflow:

**Design → Build → Validate → Document → Evidence**

Each lab contains implementation documentation and supporting evidence demonstrating the configuration and validation performed.

## Environment

- Microsoft Azure
- Azure Region: West US 2
- Resource Group: `rg-atlas-lab`
- Virtual Network: `vnet-atlas-lab-us`
- Address Space: `10.20.0.0/16`
- Server Subnet: `10.20.10.0/24`
- Client Subnet: `10.20.20.0/24`
- Domain Controller: `DC01`
- DC01 Private IP: `10.20.10.10`
- Operating System: Windows Server 2022 Datacenter: Azure Edition
- Active Directory Domain: `corp.atlas.local`
- NetBIOS Domain: `CORP`

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

**Documentation:** [`LAB-003 — Active Directory Domain Services & DNS`](docs/LAB-003-active-directory-domain-services-dns.md)

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

**Documentation:** [`LAB-004 — Active Directory Identity & Access Management`](docs/LAB-004-active-directory-identity-access-management.md)

**Evidence:** [`evidence/lab-004`](evidence/lab-004)

## Infrastructure Progress

Current environment:

**Azure Network → Windows Server DC01 → Active Directory Domain Services → DNS → Identity & Access Management**

The environment currently provides a functional Active Directory and DNS foundation for continued development of enterprise identity, access control, Group Policy, domain-joined systems, and hybrid infrastructure services.

## Technologies

- Microsoft Azure
- Windows Server 2022
- Active Directory Domain Services
- Windows DNS
- Azure Virtual Networks
- Azure Virtual Machines
- Network Security Groups
- PowerShell
- Windows administration tools
- Git
- GitHub

## Repository Structure

    atlas-hybrid-infrastructure/
    ├── docs/
    ├── evidence/
    │   ├── lab-001/
    │   ├── lab-002/
    │   ├── lab-003/
    │   └── lab-004/
    ├── LICENSE
    └── README.md

## Project Status

## Project Status

**LAB-001:** Completed  
**LAB-002:** Completed  
**LAB-003:** Completed  
**LAB-004:** Completed  

**Next Phase:** Domain-Joined Client Infrastructure & Group Policy

