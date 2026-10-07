# LAB-003 Evidence — Active Directory Domain Services & DNS

This directory contains implementation and validation evidence for the deployment of Active Directory Domain Services (AD DS) and DNS on DC01.

## Lab Objectives

- Install the Active Directory Domain Services role on DC01
- Promote DC01 as the first domain controller in a new forest
- Create the `corp.atlas.local` Active Directory domain
- Install and integrate DNS with Active Directory
- Validate the domain controller after promotion
- Verify DNS name resolution
- Validate Active Directory domain controller health
- Verify SYSVOL and NETLOGON availability

## Environment

- Server: `DC01`
- Operating System: Windows Server 2022 Datacenter: Azure Edition
- Domain: `corp.atlas.local`
- NetBIOS Domain: `CORP`
- DC01 Private IP: `10.20.10.10`
- Azure Region: West US 2

## Evidence

Screenshots in this directory document the AD DS installation, domain controller promotion, DNS configuration, and post-deployment validation.

Detailed implementation and troubleshooting documentation is maintained in the repository's `/docs` directory.
