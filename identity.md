# Azure Idenity

* AAD = Azure Active Directory
* Multi-Tenant Cloud-based directory and identuty mangement system

Difference to on prem AD:
* No support for group policies
* No organization unit or computer objects (flat hierarchy)
* No forestes, just federation

Subscription Levels:
* Free
* Basic - SLA 99.9%, Group based access management, Application Proxy
* Premium P1 - Bridges on-prem and Azure, self service, IAM, includes Identity Manager
* Premium P2 - new Identiy protection and priviledged Identity Management, monitor and restrict Administrators

Device registration, RBAC, SSPR
Manage User portal: change photo, block sign in

## Azure AD PowerShell Module

* Allows user management, domain management, SSO

Connecting
```
$msolcred = get-credential
connect-msolservice -credential $msolcred
Connect-MSOLService -AzureEnvironment "AzureGermanyCloud"
```

## Hybrid Solutions

Azure AD Connect Requirements
* Global Administrator Account
* AD Enterprise Admin
* Proper Firewall ports
* SQL Server (Express)
  * 10GB Limit = ~100,000 Objects
* Minimum 2003 forest

Azure AD replicates/enables
* Central user manamgent
* On prem Windows Server AD
* Other directories
* PC and devices
* Microsoft Apps
* Non-Microsoft Cloud Apps

Does not replicate
* OU, just the objects

## AD Syncronization

* Replicate on prem identity to Cloud
* Azure AD Connect - Sync, Federation, Health (Premium required)
* Device Writeback is possible (replicate back from Cloud to onprem, therefore r/w permission required)
* Passwords via hash sync

## Authentication Options

* SSO, Reduced Credentials
* Fewer accounts, authentication flexibility, control, claims
* MFA support

Modes:
* Cloud Identity
* Synced identity (different users but same password)
* Federated identity (same users)

## Installation of Azure AD Connect

Syncs on prem AD to Azure AD, includes AD FS, Sync and Health

Steps:
* Add Domain in Azure
* Install Azure AD Connect locally
* Select sync options
* Login to Azure AD
* Connect on-prem AD via Admin account (can add multiple)
* Select domains and OUs to sync to AAD (e.g. only users)
* Select how to identify users (GUID)
* Select filtering e.g. group
* Select features (e.g. password writeback)

## Monitoring Health

* Get AAD Premium + install Connect, but no additional configuration needed
* Possibility to set Alerts, and Notifications to email address
* Operation Insights: metrics about syncing, latencies, number of objects, etc.

## Questions

* Basic AAD doesnt have self-service group management, but has self servie password, group based application access management, no object limit
* Azure AD Connect Health from Azure Marketplace needs to be installed
* If some attributes are not syncing, use miiclient.exe for verification, or force manual sync
* All users in Marketing or Sales: Create a new Group with the Membership Type “Dynamic User”. Construct the query: (user.department -eq "Sales") -or (user.department -eq "Marketing")
* Run the Set-MsolUser PowerShell command for blocking O365 
* Connect O365 users with AAD: Logon to azure.microsoft.com, click Start for Free, and sign in with admin@contoso.onmicrosoft.com. Follow the steps on the screen
* SSPR: Before a user can use this feature, he must first define an authentication method, such as mobile number. This will be requested at the first successful login
* Priviledged global admin: Instruct the user to activate the role
* Enterprise Applications Blade: start for doing troubleshooting regarding Apps
* Conditional Access Rules require Premium Subscription
* PMI: Time-limited activation
* All featured apps support AAD Automated Provisioning

## RBAC

* RBAC groups can be defined via PS, Azure CLI or REST API
* In traditional AD, OU or groups were used, or make them Admin
* In AAD, RBAC is used, much more granular and secure
* Only used for Azure Administration, on prem used can be tied to permissions in Azure
* Supports inheritance (subscription - resource groups - resources)

Role Definitions
* Defines what access the role has

Role Assigments
* Associate role defintion with identity

High level permissions:
* Contributer, Owner, Readers
* Many more possible

/subscription/{ID}/resourceGroups/{NAME}/providers/.../sites/{SITE}

Troubleshooting via:
* RBAC Audit Logs
* Resource management locks, restricts operations to a specific scope (requires Premium)
* Change history report

## Domain Names in AAD

* New Azure subscription is based on account name and +onmicrosoft.com
* Add domain name and proof that is your namespace (via global admin)
* Can be done via classic portal or PS (not yet in new portal?)
* PS needs special Active Directory module

You can have multiple AAD in Azure:
* Live, Test, sync
* AAD is the source for O365, Intune etc if required
* Can add users from one to another AD
Create new domain:
* Search Marketplace for Active Directory
* Will bring to old portal
* will deploy and then you can add users, grous, etc.

## Managing Users and Groups

* Create in Azure instead of on-prem, will then be propagated to on-prem
* Add new user: Profile, Properties, Groups, Directory Role (User, Global Admin, Limited Admin)

# Premium Services

## Priviledged Identitiy Management (PIM)

* Monitor, restrict, discover identity access for reducing attacks, more simplification, visibility
* Example: discover user who has permission, they are not using, thus remove him from the role
* Enable time-limited roles or groups
* Can't be replicated back to on prem
* Gives alerts and control
* Requires AAD Premium

Roles managed in PIM:
* Global Admin, Privileged role admin, billing admin, password admin, service admin, user management admin, exchange admin, sharepoint admin, skype for business admin

Roles not managed in PIM:
* Roles within Exchange or sharepoint online
* Azure subscriptions and resource groups are not respresented in AAD

Implementing PIM:
* Manage priviledged roles
* Can give priviledged role for a certain time
* Audit History is showing in Azure

## Self-Service Password Reset

* Premium offering
* Self-service password reset and change, password hash synchronization, password writeback and account unlock
* SSPR for Admins (need two different verificaiton methods), e.g. call mobile phone, text mobile phone, alternate email
* SSPR for Users, only need one, including security questions

Implementing SSPR as Admin:
* Configure --> user password reset policy --> select group / restrict --> select methods and number

## Self-Service Group Management

* Users can request group ownership or being added of it via self-service
* Security groups cannot be created via SSGM
* Delegated vs self-service group management
* Configure via: Select AAD, goto group management, select which actions shall be available through SSGM
* New groups can be filled via dynamic query to add users automatically by criteria

## MFA

* Azure MFA service, comes automatically with premium
* Something you know (password), have (phone, token) or something you are (fingerprint, retina)
* How it works: Mobile Apps (push, one time passcode), phone call, text message
* Also possible to use on-prem, deployed on Windows Server
* MFA for Office 365 much more basic than Azure MFA
* Fraud Alert, One Time Bypass (e.g. for setting it up, or when phone is lost), Custom voice messages
* Configuration: Goto AAD, manage MFA, select verification options, trusted IPs for skipping MFA, app passwords
* Allows to remember MFA for x days (up to 60 days)
* Two-way SMS only available in on-prem MFA

## Application Proxy

* Enables SSO for on-prem apps
* No VPN is required, hosted in Azure, no DMZ needed, no firewall rules
* Install on-prem, Configure application address in Azure

Enabling App Proxy:
* Download AAD Application Proxy Connector and install on-prem
* In old portal, goto AD tenant, configure, application proxy, manage connectors (Application Proxy Connector), manage groups (allows groups to adminstrator connector)

## SaaS Application Integration

* Allows users to signin to cloud-based SaaS Apps with AAD credentials
* Some stuff included in Free AAD
* 2700+ apps available to connect to via SSO in gallery
* Supported for most apps, especially when using login/password
* Automated provisioning when user comes/leaves

Installation:
* Search in app catalogue, configure SSO, some provide deployment to Azure account (?)
* Goto Directory, applications, add, select from gallery, select custom if needed, add name, enable SSO, enter signin URL, check login to capture metadata, map field labels
* Then assign groups to application, upload logo, etc.
* Configure MFA or configure trusted IPs, either via MFA portal, or in application portal directly