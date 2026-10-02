# LAB-002 — DC01 Windows Server Deployment & Network Configuration

## Objective

Deploy and configure the first Windows Server virtual machine within the **Project Atlas** Azure environment.

The server, `DC01`, provides the Windows Server foundation required for the next phase of the environment, where **Active Directory Domain Services (AD DS)** and **DNS** will be implemented.

This lab focuses on:

- Azure Windows Server deployment
- Existing VNet and subnet integration
- Secure remote administration
- Network Security Group configuration
- Static private IP addressing
- Azure resource governance
- Cost-management controls
- Guest operating system validation
- Network configuration validation

---

## Architecture

DC01 was deployed into the existing Project Atlas network created during LAB-001.

```text
Azure Subscription
│
└── rg-atlas-lab
    │
    └── vnet-atlas-lab-us
        │
        ├── snet-servers
        │   └── 10.20.10.0/24
        │       └── DC01
        │           └── 10.20.10.10
        │
        └── snet-clients
            └── 10.20.20.0/24
                └── CL01 [Planned]
```

### Network Placement

| Component | Configuration |
|---|---|
| Resource Group | `rg-atlas-lab` |
| Azure Region | `West US 2` |
| Virtual Network | `vnet-atlas-lab-us` |
| VNet Address Space | `10.20.0.0/16` |
| Server Subnet | `snet-servers` |
| Server Subnet CIDR | `10.20.10.0/24` |
| DC01 Private IP | `10.20.10.10` |

DC01 is intentionally deployed within the dedicated server subnet, separating server workloads from the client subnet.

---

## Virtual Machine Configuration

| Component | Configuration |
|---|---|
| Virtual Machine | `DC01` |
| Region | `West US 2` |
| Operating System | Windows Server 2022 Datacenter: Azure Edition |
| Architecture | `x64` |
| VM Size | `Standard_B2ls_v2` |
| vCPU | `2` |
| Memory | `4 GiB` |
| Security Type | Trusted Launch |
| Secure Boot | Enabled |
| vTPM | Enabled |
| Azure Spot | Disabled |
| OS Disk | Premium SSD |
| Boot Diagnostics | Enabled |
| Auto-Shutdown | Enabled |
| Remote Administration | RDP |

---

## Resource Governance

Standardized Azure resource tags were applied during deployment.

| Tag | Value |
|---|---|
| `Project` | `Project-Atlas` |
| `Environment` | `Lab` |
| `Workload` | `Hybrid-Infrastructure` |
| `Owner` | `Shayze01` |

These tags provide consistent resource classification and support easier administration, governance, filtering, and cost visibility.

### Evidence

![DC01 Resource Tags](../evidence/lab-002/07-dc01-resource-tags.png)

---

## Pre-Deployment Validation

Before provisioning DC01, the Azure **Review + create** stage was used to validate the intended configuration.

The review confirmed:

- VM name: `DC01`
- Region: `West US 2`
- Windows Server 2022 Datacenter: Azure Edition
- VM size: `Standard_B2ls_v2`
- Trusted Launch security
- Premium SSD storage
- VNet: `vnet-atlas-lab-us`
- Subnet: `snet-servers`
- Network Security Group: `DC01-nsg`

This validation checkpoint ensured that the server would be deployed into the existing Atlas network rather than an automatically generated Azure VNet.

### Evidence

![DC01 Pre-Deployment Validation](../evidence/lab-002/08-dc01-pre-deployment-validation.png)

---

## VM Deployment

DC01 was successfully provisioned within:

```text
Resource Group: rg-atlas-lab
Region: West US 2
```

Azure confirmed successful completion of the deployment before further server configuration was performed.

### Evidence

![DC01 Deployment Success](../evidence/lab-002/09-dc01-deployment-success.png)

---

## Network Integration

DC01 was connected to the server network created during LAB-001.

```text
vnet-atlas-lab-us
└── snet-servers
    └── DC01
```

The server subnet uses:

```text
10.20.10.0/24
```

This maintains logical separation between infrastructure servers and the planned client workload subnet.

---

## Network Security

A dedicated Network Security Group was configured for DC01:

```text
DC01-nsg
```

Remote Desktop Protocol requires:

```text
Protocol: TCP
Destination Port: 3389
```

Azure initially generated an RDP rule that permitted connections from any Internet source.

Rather than retaining unrestricted Internet exposure, the rule was changed so that RDP access was limited to a single administrator source IPv4 address using `/32` CIDR notation.

```text
Administrator Public IPv4/32
        │
        │ TCP/3389
        ▼
    DC01-nsg
        │
        ▼
       DC01
```

The administrator's public IP address is intentionally excluded from this repository.

This configuration reduces unnecessary exposure of the RDP service while retaining remote administrative access to the lab server.

---

## Static Private IP Configuration

Azure initially assigned DC01 a dynamic private IPv4 address.

Because DC01 will provide infrastructure services such as **Active Directory** and **DNS**, it requires a predictable internal network address.

The primary NIC IP configuration was therefore changed from:

```text
Dynamic
```

to:

```text
Static
```

The assigned private address is:

```text
10.20.10.10
```

Final configuration:

| Property | Value |
|---|---|
| VNet | `vnet-atlas-lab-us` |
| Subnet | `snet-servers` |
| Subnet CIDR | `10.20.10.0/24` |
| IP Configuration | `ipconfig1` |
| Private IPv4 | `10.20.10.10` |
| Allocation | Static |

### Evidence

![DC01 Static Private IP](../evidence/lab-002/10-dc01-static-private-ip.png)

---

## Remote Administration

Azure Native RDP was used to establish an administrative connection to DC01.

Before downloading the RDP connection file, Azure connectivity validation confirmed that:

```text
TCP/3389
```

was accessible from the permitted administrator source address.

The downloaded RDP configuration was then opened using Windows Remote Desktop.

During the first connection, Windows displayed a certificate trust warning because the newly provisioned server was using an RDP certificate that was not issued by a certificate authority trusted by the local workstation.

After confirming that the connection targeted the intended `DC01` server, the administrative session was established successfully.

---

## Windows Server Validation

After connecting through RDP, **Server Manager** was used to validate the Windows Server guest operating system.

The following properties were confirmed:

| Property | Status |
|---|---|
| Computer Name | `DC01` |
| Operating System | Windows Server 2022 Datacenter Azure Edition |
| Workgroup | `WORKGROUP` |
| Remote Management | Enabled |
| Remote Desktop | Enabled |
| Microsoft Defender Firewall | Enabled |
| Microsoft Defender Antivirus | Enabled |
| Windows Activation | Activated |

The server remains in `WORKGROUP` at this stage because Active Directory Domain Services has not yet been installed or promoted.

### Evidence

![DC01 Windows Server Validation](../evidence/lab-002/11-dc01-windows-server-validation.png)

---

## Guest Network Validation

The network configuration was then validated directly from within the Windows Server guest operating system.

The following command was executed:

```powershell
ipconfig
```

The Ethernet adapter returned:

```text
IPv4 Address:    10.20.10.10
Subnet Mask:     255.255.255.0
Default Gateway: 10.20.10.1
```

This confirmed that the static private address configured at the Azure NIC layer was correctly presented to the Windows Server guest.

### Evidence

![DC01 IP Configuration Validation](../evidence/lab-002/12-dc01-ipconfig-validation.png)

---

## Cost Management

Because Project Atlas is a practical lab rather than a continuously running production environment, automatic VM shutdown was configured.

```text
Auto-Shutdown: Enabled
Shutdown Time: 02:30 AM
Time Zone: Dublin, Edinburgh, Lisbon, London
Notification: Enabled
```

This allows extended evening lab sessions while reducing the risk of accidentally leaving Azure compute resources running when they are no longer required.

The VM can be started manually whenever further lab work is required.

---

## Security Controls Implemented

The following controls were implemented during LAB-002:

- Trusted Launch enabled
- Secure Boot enabled
- Virtual TPM enabled
- Microsoft Defender Firewall enabled
- Microsoft Defender Antivirus enabled
- Dedicated Network Security Group
- RDP restricted to a single administrator source IP
- Public administrator IP excluded from GitHub documentation
- Static private IP assigned for infrastructure-service stability
- Azure boot diagnostics enabled
- Automatic shutdown enabled for cost control

---

## Validation Checklist

- [x] DC01 deployed successfully
- [x] Windows Server 2022 installed
- [x] DC01 deployed in `West US 2`
- [x] Connected to `vnet-atlas-lab-us`
- [x] Connected to `snet-servers`
- [x] Dedicated `DC01-nsg` configured
- [x] RDP access restricted by source IP
- [x] Remote Desktop connectivity validated
- [x] Static private IP configured
- [x] `10.20.10.10` confirmed inside Windows
- [x] Windows Server Manager validated
- [x] Microsoft Defender Firewall enabled
- [x] Resource governance tags applied
- [x] Auto-shutdown configured
- [x] Deployment evidence documented

---

## Evidence Summary

LAB-002 evidence is stored in:

```text
evidence/lab-002/
```

Evidence files:

```text
07-dc01-resource-tags.png
08-dc01-pre-deployment-validation.png
09-dc01-deployment-success.png
10-dc01-static-private-ip.png
11-dc01-windows-server-validation.png
12-dc01-ipconfig-validation.png
```

The evidence demonstrates the complete implementation path from Azure VM configuration through successful guest operating system and network validation.

---

## Skills Demonstrated

- Microsoft Azure Virtual Machines
- Windows Server 2022 administration
- Azure Virtual Networks
- Azure subnet integration
- Azure Network Interfaces
- Static IPv4 addressing
- Network Security Groups
- CIDR-based access restriction
- RDP administration
- Trusted Launch
- Secure Boot and vTPM
- Azure resource tagging
- Azure cost-management controls
- Windows Server Manager
- Windows networking
- `ipconfig` validation
- Infrastructure security
- Technical documentation
- Evidence-driven infrastructure implementation

---

## Outcome

LAB-002 successfully established **DC01** as the first Windows Server workload within the Project Atlas hybrid infrastructure environment.

The server now has:

```text
Hostname:   DC01
Private IP: 10.20.10.10
Subnet:     snet-servers
Region:     West US 2
OS:         Windows Server 2022
```

DC01 is deployed, secured, remotely manageable, network validated, and ready to provide core infrastructure services.

## Next Lab

**LAB-003 — Active Directory Domain Services (AD DS) and DNS**

The next stage will transform DC01 from a standalone Windows Server into the first domain controller and DNS server within the Project Atlas environment.
