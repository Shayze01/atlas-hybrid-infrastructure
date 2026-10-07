
# LAB-005 — Domain-Joined Client Infrastructure

**Project:** Atlas Hybrid Infrastructure  
**Lab:** LAB-005  
**Status:** Completed  
**Platform:** Microsoft Azure  
**Region:** West US 2  
**Domain:** corp.atlas.local  
**Repository:** atlas-hybrid-infrastructure

---

## 1. Lab Overview

### Objective

The objective of LAB-005 was to deploy a Windows 11 client workstation into an existing Azure-hosted Active Directory environment and establish secure, functional domain integration.

The lab builds upon the infrastructure deployed in LAB-001 through LAB-004, including Azure networking, Windows Server, Active Directory Domain Services, DNS, organisational units, domain users, and security groups.

The implementation covers:

- Azure Windows 11 virtual machine deployment
- Client and server subnet connectivity
- Custom virtual network DNS configuration
- Active Directory DNS and LDAP service discovery
- Secure remote access through Azure Bastion
- Windows workstation domain joining
- Active Directory computer object management
- Group-based Remote Desktop access
- Domain-user authentication
- Windows security event analysis
- Infrastructure troubleshooting and validation
- Technical documentation and evidence collection

### Expected Outcome

A Windows 11 workstation named `CL01` should:

1. Operate within the existing Atlas Azure virtual network.
2. Use DC01 as its DNS server.
3. Discover the `corp.atlas.local` Active Directory domain.
4. Join the domain successfully.
5. Appear within the designated Workstations organisational unit.
6. Allow an authorised domain user to authenticate remotely.
7. Successfully locate the domain controller after authentication.

**Final result:** All primary technical objectives were achieved.

---

## 2. Infrastructure Architecture

### Azure Environment

| Component | Configuration |
|---|---|
| Subscription | Azure subscription 1 |
| Resource Group | `rg-atlas-lab` |
| Region | West US 2 |
| Virtual Network | `vnet-atlas-lab-us` |
| VNet Address Space | `10.20.0.0/16` |
| Server Subnet | `snet-servers` — `10.20.10.0/24` |
| Client Subnet | `snet-clients` — `10.20.20.0/24` |
| Bastion Subnet | `AzureBastionSubnet` — `10.20.30.0/26` |
| Custom VNet DNS | `10.20.10.10` |

### Domain Controller — DC01

| Property | Configuration |
|---|---|
| Hostname | `DC01` |
| Operating System | Windows Server 2022 Datacenter: Azure Edition |
| Private IP | `10.20.10.10` |
| Subnet | `snet-servers` |
| AD Domain | `corp.atlas.local` |
| NetBIOS Domain | `CORP` |
| Roles | Active Directory Domain Services, DNS |
| Function | Domain authentication, directory services, DNS |

### Client Workstation — CL01

| Property | Configuration |
|---|---|
| Hostname | `CL01` |
| Operating System | Windows 11 Pro, version 25H2, x64 Gen2 |
| VM Size | `Standard_D2als_v6` |
| vCPU | 2 |
| Memory | 4 GiB |
| Private IP | `10.20.20.4` |
| Subnet | `snet-clients` |
| Public IP | None |
| OS Disk | Premium SSD (`Premium_LRS`) |
| Domain | `corp.atlas.local` |
| Auto-shutdown | 02:00 |
| Remote Access | Azure Bastion |

### Logical Architecture

```text
                   AZURE SUBSCRIPTION
                           |
                      rg-atlas-lab
                           |
                    vnet-atlas-lab-us
                       10.20.0.0/16
                           |
          +----------------+----------------+
          |                |                |
     snet-servers     snet-clients    AzureBastionSubnet
     10.20.10.0/24    10.20.20.0/24    10.20.30.0/26
          |                |                |
         DC01             CL01         Azure Bastion
     10.20.10.10      10.20.20.4       Basic SKU
          |                |
       AD DS/DNS      Windows 11 Pro
          |                |
          +------ Private DNS / AD --------+
                           |
                    corp.atlas.local
                           |
                Atlas / Computers /
                     Workstations
                           |
                          CL01
```

### Design Considerations

The architecture separates domain infrastructure and client workstations into dedicated subnets.

This provides a foundation for future network security controls, routing policies, workload segmentation, and infrastructure management.

CL01 was deliberately deployed without a public IP address to avoid directly exposing RDP to the internet.

Azure Bastion provides browser-based access to the workstation through the Azure virtual network.

---

## 3. Custom DNS Configuration

### Requirement

Active Directory domain joining depends on DNS.

A Windows domain client must be able to locate domain controllers and Active Directory services using DNS records, particularly SRV records.

The existing domain controller, DC01, provides DNS services for `corp.atlas.local`.

### Implementation

In the Azure Portal:

1. Opened **Virtual networks**.
2. Selected `vnet-atlas-lab-us`.
3. Opened **DNS servers**.
4. Changed the configuration from Azure-provided DNS to **Custom**.
5. Entered the DC01 private IP address.

```text
DNS Server: 10.20.10.10
```

6. Saved the configuration.

### DC01 Validation

DC01 was restarted after the DNS configuration change.

The following commands were used:

```powershell
ipconfig /all
```

```powershell
nslookup dc01.corp.atlas.local
```

The DC01 network configuration showed local loopback DNS addresses, while domain name resolution returned the expected private IP.

```text
dc01.corp.atlas.local
10.20.10.10
```

### Technical Explanation

DC01 uses its locally hosted DNS service.

CL01, as a domain client, must query DC01 for domain records.

The Azure VNet custom DNS setting provides the DNS server configuration to VMs using Azure DHCP-provided network settings.

### Evidence

- [31 — VNet Custom DNS Configuration](../evidence/lab-005/31-vnet-custom-dns-configuration.png)
- [32 — DC01 DNS Health Validation](../evidence/lab-005/32-dc01-dns-health-validation.png)

---

## 4. Windows 11 Client Deployment

### Deployment Method

Azure Portal → Virtual machines → Create → Azure virtual machine

### Basics

The following configuration was selected:

```text
Subscription: Azure subscription 1
Resource Group: rg-atlas-lab
Virtual Machine Name: CL01
Region: West US 2
Availability: No infrastructure redundancy required
Security Type: Standard
Image: Windows 11 Pro, version 25H2 - x64 Gen2
VM Size: Standard_D2als_v6
Architecture: x64
Administrator Username: azureuser
Public Inbound Ports: None
```

The Windows client licensing declaration was accepted during deployment.

### Disks

```text
OS Disk Type: Premium_LRS
OS Disk Size: 127 GiB
Delete OS Disk with VM: Enabled
Encryption: Platform-managed keys
Additional Data Disks: None
```

### Networking

During configuration, Azure initially proposed a new virtual network with an unrelated address range.

This was corrected before deployment.

Final configuration:

```text
Virtual Network: vnet-atlas-lab-us
Subnet: snet-clients
Subnet Address Range: 10.20.20.0/24
Public IP: None
Public Inbound Ports: None
Load Balancing: None
```

Accelerated networking was enabled for the selected VM size.

### Management

```text
Managed Identity: Disabled
Microsoft Entra ID Login: Disabled
Automatic Shutdown: Enabled
Shutdown Time: 02:00
Boot Diagnostics: Enabled
Site Recovery: Disabled
```

### Resource Tags

```text
Environment = Lab
Project     = Atlas
Role        = Domain-Client
Owner       = Shayze01
```

### Deployment Validation

The Azure deployment completed successfully.

The CL01 Overview page confirmed:

```text
VM Name: CL01
Status: Running
Region: West US 2
Private IP: 10.20.20.4
Virtual Network: vnet-atlas-lab-us
Subnet: snet-clients
Public IP: None
```

The Azure VM agent initially displayed a Not Ready status.

After allowing the VM to initialise and refreshing the Overview page, the agent status changed to Ready.

### Evidence

- [33 — CL01 Network Configuration](../evidence/lab-005/33-cl01-network-configuration.png)
- [34 — CL01 Pre-Deployment Validation](../evidence/lab-005/34-cl01-pre-deployment-validation.png)
- [35 — CL01 Deployment Success](../evidence/lab-005/35-cl01-deployment-success.png)

---

## 5. Azure Bastion Deployment

### Requirement

CL01 was deployed without a public IP address.

A secure method was therefore required to access the Windows desktop remotely without exposing RDP port 3389 directly to the internet.

Azure Bastion was selected for browser-based RDP access.

### Bastion Configuration

| Property | Configuration |
|---|---|
| Bastion Name | `bas-atlas-lab-us` |
| Resource Group | `rg-atlas-lab` |
| Region | West US 2 |
| SKU | Basic |
| Virtual Network | `vnet-atlas-lab-us` |
| Subnet | `AzureBastionSubnet` |
| Subnet Range | `10.20.30.0/26` |
| Public IP Resource | `vnet-atlas-lab-us-IPv4` |
| Instance Count | 2 |
| Copy and Paste | Enabled |
| IP-Based Connection | Disabled |
| Kerberos Authentication | Disabled |
| Native Client Support | Disabled |
| Session Recording | Disabled |

### Bastion Subnet Design

A dedicated subnet was assigned:

```text
Name: AzureBastionSubnet
Address: 10.20.30.0/26
```

The subnet was placed inside the existing Atlas virtual network.

This preserved the established addressing structure:

```text
10.20.10.0/24 — Servers
10.20.20.0/24 — Clients
10.20.30.0/26 — Bastion
```

### Initial Deployment Failure

The first Bastion deployment failed.

Azure reported:

```text
InvalidResourceReference
```

The deployment error stated that the referenced `AzureBastionSubnet` resource could not be found.

The public IP resource had deployed successfully, but the Bastion host deployment failed.

### Root Cause Analysis

The Bastion subnet had been defined inside the Bastion deployment wizard.

However, the subnet had not actually been created within the existing virtual network.

The Bastion deployment attempted to reference a subnet that did not yet exist as a persisted Azure resource.

### Troubleshooting Procedure

1. Opened `vnet-atlas-lab-us`.
2. Navigated to **Subnets**.
3. Reviewed the existing subnet list.
4. Confirmed only `snet-servers` and `snet-clients` existed.
5. Identified the missing `AzureBastionSubnet`.

### Corrective Action

The Bastion subnet was created directly from the VNet Subnets page.

Configuration:

```text
Subnet Purpose: Azure Bastion
Subnet Name: AzureBastionSubnet
IPv4 Address Space: 10.20.0.0/16
Starting Address: 10.20.30.0
Subnet Size: /26
NAT Gateway: None
Network Security Group: None
Route Table: None
```

The subnet was saved successfully.

The VNet Subnets page subsequently displayed all three subnets.

### Redeployment

The Bastion deployment wizard was reopened.

The existing subnet and previously created public IP resource were selected.

The SKU was explicitly verified as Basic on the final Review + Create screen.

The second deployment completed successfully.

### Technical Lessons

- Azure resource dependencies must exist before deployment references them.
- Configuration inside a deployment wizard does not necessarily mean a dependent resource has been persisted.
- Azure deployment error codes should be correlated with the actual resource state.
- Reusing successfully deployed resources avoids unnecessary duplication.
- Final SKU verification is important for cost management.

### Cost Consideration

Azure Bastion Basic incurs hourly charges while the Bastion resource exists, even when no active connection is being used.

The Bastion resource should be removed when no longer required for the lab.

### Evidence

- [36 — Bastion Pre-Deployment Validation](../evidence/lab-005/36-bastion-pre-deployment-validation.png)
- [37 — Bastion Deployment Success](../evidence/lab-005/37-bastion-deployment-success.png)

---

## 6. CL01 Network Validation

### Connection

CL01 was accessed through Azure Bastion using the local Windows administrator account:

```text
CL01\azureuser
```

The initial Windows 11 first-login configuration was completed.

### Network Validation Command

PowerShell was opened and the following command executed:

```powershell
ipconfig /all
```

### Results

```text
Host Name: CL01
IPv4 Address: 10.20.20.4
Subnet Mask: 255.255.255.0
Default Gateway: 10.20.20.1
DHCP Enabled: Yes
DNS Servers: 10.20.10.10
```

### Analysis

The results confirmed:

- CL01 was operating within the correct client subnet.
- Azure DHCP had provided the expected network configuration.
- The default gateway was correctly assigned.
- DC01 was configured as the client's DNS server.

This established the network prerequisites for Active Directory integration.

### DNS Resolution Test

The following command was executed:

```powershell
nslookup dc01.corp.atlas.local
```

Result:

```text
Name: dc01.corp.atlas.local
Address: 10.20.10.10
```

### Outcome

CL01 successfully resolved the domain controller hostname to its private IP address.

This confirmed that DNS queries from the client subnet were being answered correctly.

### Evidence

- [38 — CL01 Network and DNS Validation](../evidence/lab-005/38-cl01-network-dns-validation.png)

---

## 7. Active Directory Service Discovery

### Objective

Validate that CL01 can discover Active Directory domain controller services before attempting domain membership.

### Command

```powershell
Resolve-DnsName _ldap._tcp.dc._msdcs.corp.atlas.local -Type SRV
```

### Results

```text
Record Type: SRV
Name Target: dc01.corp.atlas.local
Priority: 0
Weight: 100
Port: 389
IPv4 Address: 10.20.10.10
```

### Technical Explanation

Active Directory publishes DNS SRV records to allow clients to locate domain controllers and directory services.

The query targeted the LDAP domain controller discovery record.

The response identified DC01 as the available domain controller and returned LDAP port 389.

### Outcome

CL01 successfully discovered the Active Directory LDAP service through DNS.

This verified the required DNS service-discovery information before domain joining.

### Evidence

- [39 — CL01 AD Service Discovery Validation](../evidence/lab-005/39-cl01-ad-service-discovery-validation.png)

---

## 8. CL01 Active Directory Domain Join

### Objective

Join CL01 to the existing Active Directory domain:

```text
corp.atlas.local
```

### Procedure

On CL01:

1. Opened Windows System Properties.
2. Navigated to Computer Name.
3. Selected Change.
4. Selected Domain.
5. Entered `corp.atlas.local`.
6. Submitted the domain join request.

### Initial Authentication Issue

The first domain join attempt used:

```text
CORP\Administrator
```

Windows rejected the credentials.

### Investigation

On DC01, the following command was executed:

```powershell
Get-ADUser Administrator |
Select-Object Name,SamAccountName,Enabled
```

The command returned an object-not-found error.

The Domain Admins group was then inspected:

```powershell
Get-ADGroupMember "Domain Admins" |
Select-Object Name,SamAccountName,ObjectClass
```

The result identified `azureuser` as the domain administrative account.

### Resolution

The domain join was retried using:

```text
CORP\azureuser
```

The correct domain administrator credentials were supplied.

Windows displayed:

```text
Welcome to the corp.atlas.local domain.
```

The workstation was restarted to complete the domain join.

### Outcome

CL01 successfully became a member of the `corp.atlas.local` Active Directory domain.

### Evidence

- [40 — CL01 Domain Join Success](../evidence/lab-005/40-cl01-domain-join-success.png)

---

## 9. Active Directory Computer Object Management

### Objective

Place the newly joined workstation into the appropriate organisational unit.

### Initial State

After domain joining, CL01 appeared in the default Active Directory Computers container.

```text
corp.atlas.local
└── Computers
    └── CL01
```

### Procedure

On DC01:

1. Opened Active Directory Users and Computers.
2. Navigated to the default Computers container.
3. Located CL01.
4. Right-clicked CL01.
5. Selected Move.
6. Navigated to the Atlas organisational unit structure.
7. Selected Workstations.
8. Confirmed the move.

### Final OU Structure

```text
corp.atlas.local
└── Atlas
    └── Computers
        ├── Servers
        └── Workstations
            └── CL01
```

### Design Rationale

The Workstations OU provides a dedicated management scope for domain-joined client computers.

This enables future workstation-specific Group Policy Objects to be linked to the OU without applying the same policies to domain controllers or servers.

### Outcome

CL01 was successfully moved into the Workstations OU.

### Evidence

- [41 — CL01 Workstations OU Validation](../evidence/lab-005/41-cl01-workstations-ou-validation.png)

---

## 10. Group-Based Remote Desktop Access

### Objective

Allow an authorised Active Directory user to connect to CL01 through Azure Bastion.

The selected domain user was:

```text
alex.morgan@corp.atlas.local
```

The user had previously been created in LAB-004 and added to the security group:

```text
GG-IT-Users
```

### Initial Problem

Bastion rejected the domain-user login.

The initial investigation examined account status, password state, domain authentication, and remote logon permissions.

### Local Remote Desktop Group Inspection

On CL01:

```powershell
net localgroup "Remote Desktop Users"
```

The initial output showed no members.

### Group-Based Access Implementation

Rather than adding Alex Morgan directly, the existing Active Directory security group was added to the workstation's local Remote Desktop Users group.

Command:

```powershell
net localgroup "Remote Desktop Users" "CORP\GG-IT-Users" /add
```

Result:

```text
The command completed successfully.
```

### Membership Verification

```powershell
net localgroup "Remote Desktop Users"
```

The output showed:

```text
CORP\GG-IT-Users
```

A further check was performed:

```powershell
Get-LocalGroupMember -Group "Remote Desktop Users"
```

Result:

```text
ObjectClass: Group
Name: CORP\GG-IT-Users
PrincipalSource: ActiveDirectory
```

### Technical Rationale

This implements group-based access management.

Access is controlled through Active Directory security group membership rather than direct assignment to an individual user.

This approach simplifies future administration and demonstrates a reusable access-control pattern.

### Outcome

The IT security group was successfully granted membership in CL01's Remote Desktop Users group.

Further authentication troubleshooting was still required before the domain user could connect successfully.

### Evidence

- [42 — CL01 RDP Group Access Validation](../evidence/lab-005/42-cl01-rdp-group-access-validation.png)

---

## 11. Domain Authentication Troubleshooting

### Problem Statement

A domain user was unable to establish an RDP session to CL01 through Azure Bastion.

The Bastion connection displayed a generic error indicating that the target machine might be unreachable or the username/password might be incorrect.

The issue persisted after group-based RDP access was configured.

### Troubleshooting Objective

Determine whether the failure originated from:

- Active Directory account status
- Invalid credentials
- Password change requirements
- Domain authentication
- Remote Desktop permissions
- Windows logon rights
- Remote Desktop Services
- Existing sessions
- Bastion credential formatting

### 11.1 Active Directory Account Health

On DC01:

```powershell
Get-ADUser alex.morgan -Properties Enabled,LockedOut,PasswordExpired,PasswordLastSet |
Select-Object SamAccountName,Enabled,LockedOut,PasswordExpired,PasswordLastSet
```

The output confirmed:

```text
SamAccountName: alex.morgan
Enabled: True
LockedOut: False
PasswordExpired: False
```

The account was enabled and not locked out.

The user had initially been configured to change the password at the next logon.

That setting was removed during troubleshooting.

### 11.2 Credential Validation on DC01

The following command was tested:

```powershell
runas /user:CORP\alex.morgan cmd
```

Result:

```text
RUNAS ERROR: Unable to run - cmd
1385: Logon failure: the user has not been granted
the requested logon type at this computer.
```

### Analysis

This was a logon-right failure on the domain controller.

Ordinary domain users are not normally granted interactive logon access to domain controllers.

No additional DC01 logon permissions were granted.

The investigation was redirected to CL01, where the domain user was intended to sign in.

### 11.3 Credential Validation on CL01

On CL01:

```powershell
runas /user:CORP\alex.morgan cmd
```

A new Command Prompt window opened successfully.

### Analysis

This confirmed that Alex's supplied credentials were accepted for a secondary logon on CL01.

It also demonstrated that the domain workstation could authenticate the domain identity.

The failure therefore required further investigation of the remote logon process.

### 11.4 Domain Membership Verification

On CL01:

```powershell
whoami
```

Initial local administrator session:

```text
cl01\azureuser
```

Domain membership was checked using:

```powershell
systeminfo | findstr /B /C:"Domain"
```

Result:

```text
Domain: corp.atlas.local
```

This confirmed that CL01 remained joined to the correct domain.

### 11.5 Remote Desktop Group Verification

```powershell
Get-LocalGroupMember -Group "Remote Desktop Users"
```

Result:

```text
CORP\GG-IT-Users
```

The group membership was correctly configured.

### 11.6 Windows Remote Logon Rights

Local security policy was exported:

```powershell
secedit /export /cfg C:\Windows\Temp\secpol.cfg
```

The following command inspected the relevant rights:

```powershell
Select-String -Path C:\Windows\Temp\secpol.cfg -Pattern "SeRemoteInteractiveLogonRight","SeDenyRemoteInteractiveLogonRight"
```

Result:

```text
SeRemoteInteractiveLogonRight = *S-1-5-32-544,*S-1-5-32-555
```

SID interpretation:

```text
S-1-5-32-544 = Administrators
S-1-5-32-555 = Remote Desktop Users
```

No explicit remote-interactive deny assignment appeared in the exported results.

### Analysis

The local security policy permitted members of the Remote Desktop Users group to log on remotely.

The evidence did not identify a missing local remote logon permission.

### 11.7 Remote Desktop Services Validation

The Remote Desktop Services service was checked:

```powershell
Get-Service TermService
```

Result:

```text
Status: Running
Name: TermService
```

TCP port 3389 was checked:

```powershell
Get-NetTCPConnection -LocalPort 3389 -State Listen
```

Result:

```text
0.0.0.0:3389 — Listen
[::]:3389 — Listen
```

### Analysis

Remote Desktop Services was running.

The workstation was listening for RDP connections on IPv4 and IPv6.

This reduced the likelihood that the failure was caused by a stopped RDP service or an inactive local RDP listener.

### 11.8 Existing RDP Session Inspection

The following command was executed:

```powershell
quser
```

Result:

```text
USERNAME    SESSIONNAME    ID    STATE
azureuser   rdp-tcp#0      2     Active
```

An active local administrator RDP session was present.

No session was terminated during this diagnostic step.

### 11.9 Windows Security Event Analysis

Windows Security Event ID 4625 was examined.

Command:

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 5 |
Format-List TimeCreated,Message
```

#### Initial Failure

The first relevant event showed:

```text
Account Name: alex.morgan
Failure Reason: The specified account's password has expired.
Status: 0xC0000224
```

### Interpretation

Status `0xC0000224` indicates that a password change is required.

The Active Directory password-change requirement was reviewed.

The account password was subsequently reset, and the first-logon password-change requirement was left disabled for the remote-login test.

#### Subsequent Failure

A later event showed:

```text
Account Name: CORP\alex.morgan
Account Domain: -
Failure Reason: Unknown user name or bad password.
Status: 0xC000006D
Sub Status: 0xC0000064
Source Network Address: 10.20.30.4
```

### Interpretation

The substatus `0xC0000064` indicates that the username was not recognised in that authentication attempt.

The source IP belonged to the Bastion subnet.

The account name appeared in the event as `CORP\alex.morgan`, while the account domain field was not populated.

This suggested a possible username-format interpretation issue in the Bastion authentication path.

### 11.10 Successful Resolution

The Bastion username was changed from:

```text
CORP\alex.morgan
```

to the user's UPN:

```text
alex.morgan@corp.atlas.local
```

The current domain-user password was supplied.

**The Bastion connection succeeded.**

The Windows 11 desktop opened under the domain user.

### Root Cause and Findings

The investigation identified two relevant authentication issues:

1. An earlier password-change-required state, evidenced by status `0xC0000224`.
2. A subsequent username-not-recognised failure, evidenced by substatus `0xC0000064`.

The final successful connection used the UPN username format.

The evidence supports that the UPN format resolved the remaining authentication failure in this specific Bastion connection flow.

It does not establish that the `DOMAIN\username` format is universally unsupported by Azure Bastion.

### Troubleshooting Lessons

- Generic connection errors should not be treated as definitive root-cause information.
- Windows Security Event ID 4625 provides more specific authentication failure codes.
- Password validity and logon permissions are separate concerns.
- Domain controller logon restrictions should not be weakened to troubleshoot workstation access.
- Active Directory group membership should be verified independently of user authentication.
- A running RDP service does not guarantee successful authentication.
- Username format can affect how credentials are interpreted by a connection interface.
- Changes should be made incrementally and validated against new event evidence.

---

## 12. Final Domain Authentication Validation

### Objective

Confirm that the domain user successfully authenticated to CL01 and that the workstation could locate its domain controller.

### Authenticated User

```text
alex.morgan@corp.atlas.local
```

### Commands Executed

```powershell
whoami
hostname
systeminfo | findstr /B /C:"Domain"
nltest /dsgetdc:corp.atlas.local
```

### Results

#### Authenticated Identity

```text
corp\alex.morgan
```

#### Workstation Hostname

```text
CL01
```

#### Domain Membership

```text
Domain: corp.atlas.local
```

#### Domain Controller Discovery

```text
DC: \\DC01.corp.atlas.local
Address: \\10.20.10.10
Dom Name: corp.atlas.local
Forest Name: corp.atlas.local
Dc Site Name: Default-First-Site-Name
Our Site Name: Default-First-Site-Name
```

The `nltest` output also identified domain controller capabilities including:

- PDC
- Global Catalog
- LDAP
- Kerberos KDC
- DNS
- Writable Domain Controller

The command completed successfully.

### Validation Summary

| Validation | Result |
|---|---|
| CL01 deployed successfully | Passed |
| CL01 assigned correct private IP | Passed |
| DC01 configured as client DNS | Passed |
| DC01 hostname resolution | Passed |
| LDAP SRV record discovery | Passed |
| CL01 domain join | Passed |
| CL01 moved to Workstations OU | Passed |
| IT security group granted RDP access | Passed |
| Domain user authentication | Passed |
| Domain controller discovery | Passed |

### Evidence

- [43 — CL01 Domain Authentication Validation](../evidence/lab-005/43-cl01-domain-authentication-validation.png)

---

## 13. Evidence Register

The following evidence was captured during LAB-005.

| No. | Evidence File | Description |
|---|---|---|
| 31 | `31-vnet-custom-dns-configuration.png` | Custom VNet DNS configuration |
| 32 | `32-dc01-dns-health-validation.png` | DC01 DNS health and resolution |
| 33 | `33-cl01-network-configuration.png` | CL01 Azure networking |
| 34 | `34-cl01-pre-deployment-validation.png` | VM configuration review |
| 35 | `35-cl01-deployment-success.png` | Successful VM deployment |
| 36 | `36-bastion-pre-deployment-validation.png` | Bastion configuration review |
| 37 | `37-bastion-deployment-success.png` | Successful Bastion deployment |
| 38 | `38-cl01-network-dns-validation.png` | Client IP and DNS resolution |
| 39 | `39-cl01-ad-service-discovery-validation.png` | Active Directory LDAP SRV discovery |
| 40 | `40-cl01-domain-join-success.png` | Successful domain join |
| 41 | `41-cl01-workstations-ou-validation.png` | AD computer object OU placement |
| 42 | `42-cl01-rdp-group-access-validation.png` | Group-based RDP access |
| 43 | `43-cl01-domain-authentication-validation.png` | Domain authentication and DC discovery |

### Evidence Directory

[View LAB-005 Evidence](../evidence/lab-005/)

All evidence files are stored in the dedicated LAB-005 evidence directory.

---

## 14. Skills Demonstrated

### Microsoft Azure

- Virtual machine provisioning
- Azure virtual networking
- Subnet planning and IP addressing
- Custom DNS configuration
- Azure Bastion deployment
- Private virtual machine administration
- Azure deployment troubleshooting
- Resource dependency validation
- Resource tagging
- Infrastructure cost awareness

### Windows Server and Active Directory

- Active Directory Domain Services integration
- DNS-based domain controller discovery
- LDAP SRV record validation
- Domain joining
- Computer account administration
- Organisational unit management
- Active Directory security groups
- Group-based remote access
- Domain-user authentication

### Windows Administration

- Windows 11 configuration
- PowerShell diagnostics
- Windows network configuration
- Windows logon rights
- Remote Desktop Services
- Local security group management
- Windows Security Event ID 4625
- Authentication status-code interpretation
- Domain controller discovery using `nltest`

### Infrastructure Engineering Practices

- Structured troubleshooting
- Root-cause analysis
- Configuration validation
- Security-conscious access design
- Least-privilege-oriented group administration
- Evidence collection
- Technical documentation
- Repeatable infrastructure procedures

---

## 15. Issues and Resolutions Summary

| Issue | Root Cause / Finding | Resolution |
|---|---|---|
| Azure Bastion deployment failed | Bastion subnet did not exist in the VNet | Created `AzureBastionSubnet` directly in the VNet |
| Domain join credentials rejected | Incorrect domain administrative account used | Identified and used `CORP\azureuser` |
| Alex could not remotely log in | Remote access group membership initially missing | Added `CORP\GG-IT-Users` to Remote Desktop Users |
| DC01 `runas` returned error 1385 | Requested logon type not granted on the domain controller | Tested authentication on CL01 instead |
| Bastion reported login failure | Earlier password-change-required state | Reset password and cleared first-logon change requirement |
| Bastion continued rejecting login | Subsequent username-not-recognised event | Used `alex.morgan@corp.atlas.local` UPN format |
| Bastion login succeeded | Valid credentials accepted using UPN | Verified authenticated domain session |

---

## 16. Final Outcome

LAB-005 successfully established a Windows 11 client workstation within the existing Atlas hybrid infrastructure lab environment.

The workstation was:

- Deployed into the designated Azure client subnet.
- Configured to use DC01 as its DNS server.
- Validated for DNS and Active Directory service discovery.
- Joined to the `corp.atlas.local` domain.
- Organised within the Workstations OU.
- Configured for group-based remote access.
- Successfully accessed using an authenticated Active Directory user.
- Validated for domain membership and domain controller discovery.

The lab also demonstrated practical troubleshooting across Azure networking, deployment dependencies, Windows authentication, Active Directory, and Remote Desktop Services.

### Final Status

**LAB-005 — COMPLETED**

### Next Lab

**LAB-006 — Group Policy Management and Workstation Security**

The next lab will build on the domain-joined CL01 workstation to configure, apply, and validate Active Directory Group Policy Objects.

This will extend the Atlas environment from identity and domain integration into centralised workstation configuration and security management.
