# LAB-004 Evidence — Active Directory Identity & Access Management

This directory contains implementation and validation evidence for Active Directory identity and access management within the `corp.atlas.local` domain.

## Lab Objectives

- Design an enterprise-style Organizational Unit (OU) structure
- Separate users, computers, groups, and service accounts
- Organize user accounts by department
- Create departmental Active Directory user accounts
- Create Global Security Groups for departmental access management
- Assign users to the appropriate security groups
- Validate security group membership using PowerShell

## Active Directory Structure

The `Atlas` OU was created as the administrative structure for lab-managed Active Directory objects.

The following structure was implemented:

    Atlas
    ├── Users
    │   ├── IT
    │   ├── Finance
    │   └── HR
    ├── Computers
    │   ├── Workstations
    │   └── Servers
    ├── Groups
    └── Service Accounts

## Departmental Users

The following lab user accounts were created:

- Alex Morgan — IT
- Sarah Williams — Finance
- Emily Carter — HR

## Security Groups

The following Global Security Groups were created:

- `GG-IT-Users`
- `GG-Finance-Users`
- `GG-HR-Users`

Users were assigned to their corresponding departmental security groups.

## Validation

Security group membership was validated through Active Directory Users and Computers and independently verified using the Active Directory PowerShell module.

## Evidence

Screenshots in this directory demonstrate:

- Organizational Unit structure
- Departmental user provisioning
- Security group configuration
- User-to-group membership
- PowerShell-based membership validation

Detailed implementation documentation is maintained in the repository's `/docs` directory.
