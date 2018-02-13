# Azure App Service

Five main components:
* Web Apps
* Mobile Apps
* Logic Apps - Automating Business processes without code
* API Apps - hosting API Endpoints
* Functions - Serverless

Features:
* First class support for: ASP.NET, Node.js, Java, PHP, Python
* Visual Studio Team Services, GitHub, BitBucket
* A/B Testing
* Global Scale
* SaaS Connectors for SAP, Oracle, SFDC, O265, Facebook, Twitter
* Security and compliance
* App templates
* Visual Studio integration
* Sticky sessions for easy migration of legacy apps
* ISO, PCI and SOC compliance

App Service Plans define:
* Region
* Scale count
* Instance Size
* SKU

Apps in same plan can share resources to save cost

App Service vs Service Fabric:
* App Service for web apps, background jobs, migration tools
* Service Fabric: Microservice framework, stateful options, WebAPI, WIN, ASP.NET Core
* VM is less abstract than App Service is less abstract than Service Fabric

Mobile Apps:
* Native and cross platform supported (iOS & Android)
* Offline ready apps
* Push Notifications
* SSO
* Auto scaling
* Staging environements
* CI/CD
* Virtual networking supported
* Isolated or dedicated environements

API Apps:
* Allows hosting of APIs in cloud or on prem
* Security, access control, hybrid, SDK generation, integration into logic apps
* Import existing APIs or develop using ASP.NET, C#, Java, PHP, Node.js, Python

Logic Apps:
* Implement scalable workflows without writing code
* Similar to IFTTT, but for enterprises
* Integrats with custom APIs, Functions and Service Bus

On-prem apps can be connected via Azure Service Bus Relay for allowing hybrid connections. Uses Hybrid Connection Manager on prem.

## Deploying Apps

Deployment is possible via:
* FTP or FTPS
* Kudu with backend of Git, Mercurial, Dropbox or OneDrive, VSTS, local Git
* Web Deploy

### Kudu

* Engine behind source control based deployments into App Service
* Also offers debugging capabilities for env variables, files, logs, etc.
* Has built-in console
* Access via Advanced Tools in Azure Console, direct console and log acces
* Upload files via drag and drop, if you drop to Size column, it will auto-extract it

### Continuous Deployment

* Create deployment slot (another app linked to main app, e.g., staging, dev, etc.)
* Configure via: App Deployment -> Deployment options -> Select source (e.g., GitHub) and branch
* Works with public and private repos
* Deployment details shows commands, e.g., `deploy.cmd`
* Standard or Premium App Plans have multiple deployment slots for swapping staging to prod (auto swap is possible)
* Non production slots cannot be scaled
* Allows reverting if deployment failed/not good
* Staging good for warming up app if needed
* GitHub connection stays correct when staging and production are swapped
* Swap: Type, Source, Destination need to be set
* Also possible: Prod, Staging, LastKnownGood Slot
* Once swapped, you need to resync to branch

### ARM - Azure Resource Manager

* Can deploy many resources at once into resource group
* Resource group should include all resources belonging to an app that have the same lifecycle
* Resources can move from one group to another and have different regions
* Resources can be linked from one group to another if they need to interact but have different lifecycles (e.g., app and database)
* Tags: 15 tags, 512 char name, 256 char value
* Put all resources needed for app into one ARM template
* Resources are created in parallel when depoyed

ARM Template:
* Schema
* Content Version
* Parameters
* Variables
* Resources
* Outputs

```
{
   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "",
   "parameters": {  },
   "variables": {  },
   "resources": [  ],
   "outputs": {  }
}
```

Deploy via Template Hub or PowerShell:
`New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>`

## Managing, Scaling and Monitoring Apps

Manage via:
* PowerShell, Azure CLI, REST APIs, Azure Portal

App Settings:
* App Settings
  * Versions of programming language
  * Platform
  * Websockets
  * Always on
  * Auto-swap
  * ARR Affinity
  * Debugging for remote debugging via Visual Studio
  * App Setting key-value pairs (env variables)
    * Slot Setting - if marked, settings stay with the slot
  * Connection String, also for stage/prod
  * Default documents: Define the files that App Service looks for
  * Handler Mappins
  * Virtual applications and directories
* Authentication
* Backups
* Custom Domains
* SSL Certs
* Networking
* Scaling
* Security Scanning
* WebJobs
* MySQL in App
* Properties
* Locks
* Automation scripts

Using `ApplicationHost.config` in web app allows to add extensions.

### App cloning

* App can be cloned to e.g., different region
* Only works in premium plan
* Includes API Apps and mobile apps
* Not all settings are cloned, e.g. DB content, traffic manager settings, etc.

### Scaling Apps

* Scale up
* Scale out
* Scale global

Isolation options:
* Everything shared (free and shared plans)
* Share some things (Basic, Standard, Premium)
  * Example: LBs are shared
* Share nothing (App Service Environements)
  * Front end, infra, workers (e.g. LB) 
  * Useful for hyperscale apps
  * Kind of like a "private region"

### App Service Plans

* CPU, Storage, Location, Mem, Network
* Standard, Basic, Premium have same machines, but different features
* App config, content, code is decoupled, hence app plans
* Scale up, e.g., go from S1 to S2 (connections get drained after a 120s, and then move over)
* Scale out, increase number of instances

### Stateless apps

* Move session state to e.g. Redis to move app towards stateless
* ARR Affinity on per default, but better to switch off if app is stateless (= better performance)
* ARR auto-creates cookie and routes to same server

### Auto-scaling

* Manual
* Scheduled - for example by time, e.g. planned event or in the evening
* Metrics-based - by one or more metrics (check in dashboard first)
  * CPU, Memory, Disk queue, HTTP Queue, Data In/Out
* Metrics and schedule can be combined

### Geo-distribution

* Scale to multiple regions for availablity, scale, and DR
* Drawbacks: Data replication and more complex architecture
* Have single point of entry

Configuration:
* Traffic Manager routes traffic between different deployments
* DNS -> CDN -> Traffic Manager
* Modes: Priority-based (failover) routing, weighted-based, Performance-based

### App Service Environements

* Up to 50 instances (soft)
* Frontend can be scaled independant of backend (e.g., for handling SSL traffic)
* vNET/subnet support for VPN/on-prem connection
* New scale button for scale frontend and worker pools seperately
* Can specify inbound traffic rules (via vNET option)
* Seperate to normal Web Apps when deploying from Portal

### Load Testing

* Understand what can break first
* Performance test section, returns metrics like users/s, latency, etc.
* Impacts auto-scaling settings, so app might scale out
* Define users and duration
* Can be triggered automatically after checkin via CI pipeline (manually)

### Monitoring

* Monitoring HTTP Traffic via Diagnose and solve problems
  * Shows number of requests and 4xx and 5xx error rates
* Identify instances with poor performance
  * Shows view of instance metrics: IO, threads, etc.
* Advanced App restart
  * Allows manual restarting off instance VMs
* Monitor CPU per instance (not per App) - for identifying noisy neighbors for example
* Event Logging (needs to be turned on)
  * Logs all events including execptions
* Failed request tracking
  * Tracks per request the type and a lot of metrics including timestamps and duration
* Process Explorer
  * Kind of like a taskmanager for apps

### Troubleshooting

* Resource Health Check
* Diagnose and solve problem field, does a check and presents common solutions
* Application Failure History
  * Long term logging of status events, good for correlation of events
* Diagnostics as a Service
  * Automatically grabs all logs from instances and runs diagnostics on it
  * Generates reports for developers

### App Service Mitigate

* Autoheal can be turned on/off
* Set rules (amount of requests, slow requests, etc.), and then triggers actions
* Actions: recycle, log event, custom action (e.g. run memory dump via executable)
* Mutiple rules against one action

## Security

### Authentication and Authorization

Authentication:
* Azure AD, Facebook, Google, Microsoft Account, Twitter
* Register app at identity provider, then put ids and secrets into app service
* URL: `{your App Service base URL}/.auth/login/<provider>`, where `<provider>` is one of the following values: aad, facebook, google, microsoft, or twitter
* Cookie is set, or JSON web token (JWT)

Alternatively:
* Inbound certificate-based authentication (client needs to present a specific certificate)
* Outbound certificates (for talking to websites that require you to have a specfic certificate)

Authorization:
* Allow only authenticated requests
* Forward to login page
* Authentication via HTTP header, and handle it in application code
* Turn off
* Configure via "Action to take when request is not authenticated"
  * Log in with provider name
  * Allow requests (no action), defers decision to your code
  * Disable completely and have app handle it

### Security features

Static IP restriction:
* Disallow certain IP addresses
* Enables geofencing

Dynamic IP restriction:
* Request frequency per client IP address
* Concurrency per client IP address

Security Scanning feature to detect security flaws in app:
* Automatically tests XSS, SQL Injection, etc.
* Paid by credit card

## Webjobs and Functions

Azure Scheduler also exists, mostly a powerful scheduler to do something

### Webjobs

* On Demand
* Continuously
* On schedule
* Can run cmd, bat, exe, ps1, sh (Bash), php, py, js, jar

### Functions

* Serverless
* C#, F#, Node.js, Python, Php, Batch, Bash, etc.
* Can be protected via identity provider
* Functions runtime is open source on GitHub
* Support NuGet and NPM

Can be triggered from:
* BlobTrigger
* EventHubTrigger
* Generic webhook
* GitHub webhook
* HTTPTrigger
* QueueTrigger
* ServiceBusQueueTrigger
* ServiceBusTopicTrigger
* TimeTrigger