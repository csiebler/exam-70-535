## Azure SQL

Active geo-replication
* Up to 4 read-only copies within same subscription but different region

Auditing and threat detection
* Logs to Table or Blob storage
* Audits: Plain SQL, Parameterized SQL, Stored Procedure, Login, Transaction Management 
* Threat detection: SQL Injection, SQL Injection Vulnerability, Login from new location, Unusual usage pattern
* Set on database level, not server

Alerts
* When certain database metric is over a threshold for a certain period 
* Sends emails, trigger webhook, etc.

Dynamic management views
* Allows to query metrics within the database
* More sophisticated than just alert data

## Azure SQL Data Warehouse

* Scale out data warehouse
* PaaS Service
* Compute provisioned via DWU (Data Warehouse Unit)
* Control Node distributed queries to compute nodes
* Increasing DWU increases number of compute nodes in backend

Getting data into SQL Data Warehouse
* Single-client workloads: SQL Server Integration Services (SSIS), Azure Data Factory, Bulk Process Copy (BCP) - traffic always goes through Control Node
* Parallel reader loading methods: Polybase - loads data from Blob in parallel into SQL DW, bypasses Control Node

Working with data
* Columns need to be indexed and distributed accross nodes by hashing it or partioning it by e.g., date
* PowerBI can connect into DW, easily accessible via Azure Portal
