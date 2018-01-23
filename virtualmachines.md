# Virtual Machines

Physical, Virtual, IaaS, PaaS, SaaS
Azure VMs are part of IaaS layer

## Overview

Uses:
* Dev/Test, Apps in Cloud, Extend Datacenter

VM Chacteristis:
* Name (up to 15 characters)
* Location
* Size/Type
* VM Quota Limits
* Disks and Images (from marketplace or your own images)
* Extensions
* Availability (distribute in separate availability sets)
* Backup

VM Series:
* A - entry
* D - general purpose, balanced CPU/memory ratio
* F - compute optimized
* G - memory and storage optimized
* H - high permformance
* L - storage optimized
* N - GPU enabled

Or in words:
* General Purpose, Compute Optimized, Memory optimized, Storage optimized, GPU, High Performance Compute

Access:
* SSH, RDP, WinRM

Network:
* Site to site VPN, vNET, ExpressRoute

Difference Hyper-V and Azure
* Azure no direct console access, No Gen 2 Hyper-V VM fetaures, No VHDX support, No guest OS Upgrade, multiple NICs depends on VM size

No good fits:
* Low volume, limited growth workloads (on prem cheaper)
* Regulated environements

Move VMs to Azure:
* Check via Azure Virtual Machine Readiness Assessment tool
* Azure Virtual Machine Optimization Assesment software

Sizing:
* A1 smallest size for production

## Creating VMs

Options for deploying VMs are:
* Azure Portal
* Templates
* PowerShell
* Visual Studio
* Azure CLI

Image Types:
* VM Image (includes all attach disks)
* OS Image (bare OS)

Sources:
* Azure Marketplace
* VM Depot
* Custom Image

Creating Images:
* From VM, or Azure VM
* Using sysgrep for generalization

Linux:
* Endorsed - SLA for uptime
* Non-endorsed




## Questions:
* Azure VMs dont support Multipath IO and Network Load Balancing
* Generation 1 VMs are supported on Azure
* Cloud Service can have up to 50 VMs
* A1 smallest VM suited for production
* Hyper-V is not supported on Azure VMs
* ACU = Azure Compute Unit for sizing performance
* Standard A4 has 8 cores
