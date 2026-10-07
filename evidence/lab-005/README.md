# LAB-005 — Domain-Joined Client Infrastructure

## Objective

Deploy and integrate a Windows 11 client workstation into the Atlas Active Directory environment, validating DNS-based domain discovery, domain membership, Active Directory organisational placement, group-based remote access, and domain-user authentication.

## Environment

- Client: CL01
- Operating System: Windows 11 Pro
- Client IP: 10.20.20.4
- Client Subnet: snet-clients (10.20.20.0/24)
- Domain Controller: DC01
- Domain Controller IP: 10.20.10.10
- Domain: corp.atlas.local
- Target OU: Atlas → Computers → Workstations
- Remote Access: Azure Bastion
- Authorised AD Group: GG-IT-Users

## Evidence

31. VNet custom DNS configuration
32. DC01 DNS health validation
33. CL01 network configuration
34. CL01 pre-deployment validation
35. CL01 deployment success
36. Azure Bastion pre-deployment validation
37. Azure Bastion deployment success
38. CL01 network and DNS validation
39. Active Directory service discovery validation
40. CL01 domain join success
41. CL01 Workstations OU validation
42. Group-based Remote Desktop access validation
43. Domain authentication and domain controller discovery validation

## Outcome

CL01 was successfully deployed into the client subnet and configured to use DC01 as its DNS server.

The workstation successfully discovered Active Directory services, joined the corp.atlas.local domain, and was placed in the Atlas Workstations OU.

Remote access was assigned through the GG-IT-Users Active Directory security group rather than directly to an individual user.

Domain user Alex Morgan successfully authenticated to CL01, and domain controller discovery confirmed DC01 as the servicing domain controller.
