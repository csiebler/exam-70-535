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

### PowerShell

* First create vnet, nic, etc.
```
$cred = Get-Credential
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
$vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id 
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

### Resource Manager Templates (ARM Templates)

1. Create template
1. Create parameters file
1. Create Resource Group
1. `New-AzureRmResourceGroupDeployment` or `az group deployment create`

* Start by using the Azure Quickstart templates
* Goto desired resource, select `Automation Script` and copy ARM template definition

### Custom Images

Deploy VMs build from on-prem Windows VMs:
1. Prepare the VM via `sysprep` in order to generalize VM
1. Prepare the VM VHD (only gen 1 supported on Azure, no VHDX, use `convert-vhd`)
1. Create Storage Container on Azure (Blob)
1. Upload VHD via e.g., `Add-AzureRmVhd`
1. Create VM from VHD

### Linux VMs

Linux subsystem on Windows can be enabled via `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

## Configuring VMs

* IP + Network - private & public IPs
  * Static vs Dynamic IPs (default for private and public IPs, can change after restart)
* Security Groups
  * Can be applied to network interfaces, subnets and vNets
* HA
* Backup
  * Uses Recovery Services Vault, different retentions possible
* Auto Shutdown
* Updates
  * Every 12h by default on Windows/Linux, check via OMS
* DR
* Inventory
* Change Tracking
  * Tracks Windows and Linux, send to Azure Analytics service(s)
* Metrics and Alters
  * Near realtime performnace data of VM, triggers scale-up/down, sends notification, takes actions (e.g., via webhooks)
* Extensions
  * Small apps that perform post-deployment tasks, e.g. Puppet, Anti-Virus, etc.
* Availability sets
  * Logical grouping for 2+ VMs, number of fault domains (1-3), update domains (protected from HW and network failure)
  * Needs to be set during VM creation
  * Just works on infrastructure layer
  * `New-AzureRmAvailabilitySet`
  * Each app tier into seperate availability set
  * SLA guarantee 99.95% if 2 or more VMs in Availability set
  * Single VM using premium storage has SLA of 99.9%

* Scale Sets
  * Azure manages scale, HA, availability during patching/updates
  * Set via Rules
  * Some products in Marketplace use scalesets by default
  * Extensions can be used to add stuff to Scale sets
  * Automatically created with LB and NAT rules for SSH/RDP connections
  * Auto creates Public IP, Loadbalancer, Instances
  * Supports CD
  * Can use various metrics for scaling
  * `Get-AzureRmVMss` gets scaleset in PS

IP Addresses:
* Public, private
* Dynamic, static

Update and Fault Domains
* Update Domain: only one update domain is rebooted at a time (default 5, up to 20)
* Fault Domain: group of VMs that share commong set of hardware, switches, and more that share a SPOF, at least 2 in Availablity Set

Azure Resource Explorer
* web-based tool for viewing and modifying Azure resources

## VM Storage

Disks used by VMs
* 2GB OS Disk
* Temporary disk for Pagefile (size automatic)
* Data Disks (user defined) - Standard, premium, managed, unmanaged

Azure Import and Export service
* Bringing large amounts of data into/outof Azure

## Managing VMs

VM Agent Extensions

Azure Cross-Platform Command-Line Interface

PowerShell
* `Move-AzureRmResource` for moving VM to different subscription
* `Update-AzureRmVM` for resizing VM (might require reboot)

RDP
* Port 3389
* Connect to public IP or DNS name

SSH


Configuration Management
* Out of the box: Puppet, Chef, DSC (Azure Desired State Configuration)
* Custom Script Extension for directly running scripts in VM

## Questions:
* Azure VMs dont support Multipath IO and Network Load Balancing
* Generation 1 VMs are supported on Azure
* Cloud Service can have up to 50 VMs
* A1 smallest VM suited for production
* Hyper-V is not supported on Azure VMs
* ACU = Azure Compute Unit for sizing performance
* Standard A4 has 8 cores
* ARM Templates can be up to 1MB
* Scale Sets are often integrated with Azure Insight Load Balancer and NAT Rules
* A7 Standard can have up to 16 data disks
* New disk to VM, you need to define Host caching and location
* Azure VPN only uses public IPs
* ARM templates require $schema and contentVersion fields
* New-AzureRMResourceGroupDeployment requires ResourceGroupName, TemplateURI, Name
* Alerts trigger when threshold crossed or when rule becomes active/gets created