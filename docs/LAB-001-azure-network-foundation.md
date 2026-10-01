# LAB-001 — Azure Virtual Network Foundation

## Project

**Project Atlas — Hybrid Infrastructure**

## Objective

Design and deploy the foundational Azure network for a fictional enterprise environment.

The objective of this lab was to create a dedicated Azure Virtual Network (VNet) and segment server and client workloads into separate subnets, providing the network foundation for later Windows Server, Active Directory, DNS, Group Policy, identity, access-control and infrastructure troubleshooting labs.

---

## Architecture

```text
Azure Subscription
│
└── rg-atlas-lab
    │
    └── vnet-atlas-lab
        │
        ├── snet-servers
        │   └── 10.20.10.0/24
        │       └── DC01 [Planned]
        │
        └── snet-clients
            └── 10.20.20.0/24
                └── CL01 [Planned]
```

---

## Configuration

| Component | Configuration |
|---|---|
| Azure Region | UK South |
| Resource Group | `rg-atlas-lab` |
| Virtual Network | `vnet-atlas-lab` |
| VNet Address Space | `10.20.0.0/16` |
| Server Subnet | `snet-servers` |
| Server Subnet CIDR | `10.20.10.0/24` |
| Client Subnet | `snet-clients` |
| Client Subnet CIDR | `10.20.20.0/24` |
| Azure Bastion | Disabled |
| Azure Firewall | Disabled |
| DDoS Network Protection | Disabled |
| NAT Gateway | Not configured at this stage |
| Network Security Groups | Not configured at this stage |

---

## Implementation

### 1. Resource Group

Created a dedicated Azure resource group:

`rg-atlas-lab`

The resource group provides a logical management boundary for the resources used throughout the Atlas hybrid infrastructure environment.

### 2. Virtual Network

Created the virtual network:

`vnet-atlas-lab`

Configured the private IPv4 address space as:

`10.20.0.0/16`

Azure initially proposed a default address space and subnet. These defaults were deliberately replaced with the addressing scheme defined for the Atlas architecture.

### 3. Network Segmentation

The VNet was segmented into two dedicated `/24` subnets.

#### Server Subnet

**Name:** `snet-servers`  
**CIDR:** `10.20.10.0/24`

This subnet is reserved for server infrastructure, beginning with the planned Windows Server `DC01`.

`DC01` will later be configured to provide Active Directory Domain Services and DNS functionality for the Atlas environment.

#### Client Subnet

**Name:** `snet-clients`  
**CIDR:** `10.20.20.0/24`

This subnet is reserved for client systems, including the planned Windows client `CL01`.

Separating server and client workloads provides a foundation for later network security, access-control and troubleshooting exercises.

---

## Validation

The VNet deployment completed successfully in Microsoft Azure.

Post-deployment validation confirmed:

- `vnet-atlas-lab` exists within `rg-atlas-lab`
- The VNet address space is `10.20.0.0/16`
- `snet-servers` is deployed as `10.20.10.0/24`
- `snet-clients` is deployed as `10.20.20.0/24`
- Both subnets are available for future workloads
- The automatically generated default subnet was removed
- The deployed configuration matches the planned Atlas network architecture

---

## Key Learning

This lab demonstrated the importance of designing cloud networking deliberately rather than relying entirely on automatically generated defaults.

Key concepts applied during the lab included:

- Azure Resource Groups
- Azure Virtual Networks
- Private IPv4 addressing
- CIDR notation
- Subnetting
- Network segmentation
- Server and client workload separation
- Azure resource deployment
- Post-deployment validation

The `10.20.0.0/16` VNet provides the overall private network address space, while dedicated `/24` subnets create logical network boundaries for different workload types.

---

## Troubleshooting and Engineering Decisions

During configuration, Azure automatically generated default networking values.

Rather than accepting these defaults, the network configuration was reviewed and changed to match the planned Atlas architecture.

The default subnet was removed and replaced with:

- `snet-servers` — `10.20.10.0/24`
- `snet-clients` — `10.20.20.0/24`

This ensured that the deployed environment matched the intended design before additional infrastructure was introduced.

---

## Evidence

Evidence was captured throughout the implementation and validation process.

### Evidence Set

**E001 — Azure Lab Environment Established**  
Azure environment successfully created and prepared for Project Atlas.

**E002 — VNet Pre-Deployment Validation**  
VNet configuration reviewed before deployment, confirming the intended address space and subnet design.

**E003 — Successful VNet Deployment**  
Azure confirmed successful deployment of `vnet-atlas-lab`.

**E004 — Deployed Subnet Validation**  
Post-deployment validation confirmed:

- `snet-servers` — `10.20.10.0/24`
- `snet-clients` — `10.20.20.0/24`

> Public evidence will be sanitised before publication to prevent unnecessary Azure account or subscription information from being exposed.

---

## Skills Demonstrated

- Microsoft Azure
- Azure Virtual Network administration
- IPv4 addressing
- CIDR and subnetting
- Network segmentation
- Cloud infrastructure design
- Azure resource management
- Infrastructure validation
- Technical documentation

---

## Result

**LAB-001 Status: COMPLETE ✅**

The Azure network foundation has been successfully deployed and validated.

The environment is now ready for the first server workload.

---

## Next Lab

### LAB-002 — DC01 Windows Server Deployment & Network Configuration

The next lab will deploy the first Windows Server virtual machine into:

`snet-servers — 10.20.10.0/24`

The server will be prepared for its later role as:

- Active Directory Domain Controller
- DNS Server
- Core identity infrastructure for the Atlas hybrid environment

---

## Project Disclaimer

This project represents a self-directed fictional enterprise lab created for technical learning and portfolio development.

It does not represent or disclose the infrastructure, systems, configurations or internal processes of any current or previous employer.
