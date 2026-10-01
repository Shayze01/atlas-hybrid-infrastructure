# LAB-001 — Evidence

## Azure Virtual Network Foundation

This directory contains sanitised implementation and validation evidence for **LAB-001 — Azure Virtual Network Foundation** within Project Atlas.

## Evidence Files

| Evidence ID | File | Description |
|---|---|---|
| E001 | `01-vnet-pre-deployment-validation.png` | Azure pre-deployment validation showing the planned VNet address space and server/client subnet configuration. |
| E002 | `02-vnet-deployment-success.png` | Azure confirmation that the virtual network deployment completed successfully. |
| E003 | `03-subnet-validation.png` | Post-deployment validation showing the deployed server and client subnets and their CIDR ranges. |

## Validated Configuration

- VNet: `vnet-atlas-lab`
- Address Space: `10.20.0.0/16`
- Server Subnet: `snet-servers` — `10.20.10.0/24`
- Client Subnet: `snet-clients` — `10.20.20.0/24`
- Region: `UK South`

## Security and Privacy

Evidence published in this repository is reviewed and sanitised before publication.

Azure subscription identifiers, credentials, personal information, internal employer information and other unnecessary account-specific data are excluded from public evidence.
