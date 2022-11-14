# Data in Azure
Azure provides many data solutions as cloud services, such as Relational databases, NoSQL databases, object stores. They are all fully managed services, and can be part of Azure app or completely independent. They have various pricing models, and are always better than unmanaged solutions, because you do not need to control the vm that holds the database, and therefore not responsible for patching, maintenance, etc. 

What to look for when selecting a database:
- Security: Network isolation, Encryption
- Backup: Backup types, Retention Period
- Availability: SLA, Replication, DR

Note that we always have the choice to install database on a VM (Azure VMs can be setup with database software). There are also ready-made VMs in the marketplace that comes with pre-installed databases, such as Oracle Database. The advantage of having database on VM is that you will have full flexibility and control to do whatever you wish. The disadvantage is that you need to take care of everything, such as SLA, updates, availability, security, backups, etc.  

## Azure SQL
Managed SQL Server on Azure, works like any other SQL Server using the same tools. Great compatibility with on-prem SQL Server (Depends on the exact Azure SQL Flavor). Offers built-in security, backups, availability and more. Flexible pricing models. Azure SQL has the following 3 flavors. 

### Azure SQL Database
One flavor of Azure SQL. Managed SQL Server on Azure. It is a single database on a single server, with automatic backups, updates, scaling. It has good compatibility with on-prem SQL Server, but not all features are supported. 

Security: IP firewall rules to allow specified IPs. Service Endpoints to connect to a VNet. SQL and Azure AD authentication. Secure communication (TLS). Data encrypted by default (TDE, aka transparent data encryption). 

Backup: Full back up each week; Differential back up every 12-24 hrs depends on configuration, Transaction Log every 5-10 min. Which means that the maximum amount of data that you can lose due to failure is in the last 5 min. 

Retention Period: Regular backup of 7-35 days (default if 7). Long term backup of up to 10 years. 

Availability: Back up is by default stored in a geo-redundant storage, have the option for active geo-replication, so that if a region shut down, you have another replication that is available for use immediately. SAL: 99.9% -99.995%, depends on the tire and redundancy. Note that the SLA is the second highest in all Azure (The highest one belongs to CosmosDB). 

Has two compute tiers: Provisioned (Pay for allocated resources regardless of actual use; can be reserved by paying upfront and get a hefty discount) and Serverless (Pay for actual use - vCore + RAM/sec; automatically paused when inactive, that is when you pay just for storage; expect a slight delay when warming up, just like what happens to a function app, but with dbs, usually this is not problem because the delay is not too long, and it should not happen many times during the day because you should encounter it at the beginning of the work day and that's it; serverless db cannot be reserved). 

Pricing: Purchase model can be in terms of DTU or vCores. Roughly, one DTU = 1 vCore. With DTU purchase model, the service tier can be Basic, Standard, and Premium - they differ in terms of performance and SLA. With vCores purchase model, the service tier can be General Purpose, Business Critical, and Hyperscale - they can be either serverless or provisioned. 

This is what most clients choose to use out of all 3 flavors. 

### Elastic Pool
It is the second flavor of Azure SQL. It is based on Azure SQL, but allows storing multiple databases on single server. It is great for databases with low average utilization and infrequent spikes. For example, you have 4 databases that each have spikes at different times of the day, you can then put all these 4 dbs into a single server, because it is less likely that all of them will be spiking at a point in time and need more than what a server could provide. 

Elastic pool is very cost effective. You purchase only the compute resources you need, and not the database itself. You can put on it as many databases as you want, as long as the resources of the server can support this number of databases.

Pricing is similar to Azure SQL Database. 

### Managed Instance 
It is the third flavor of Azure SQL. Managed Instance is closest to the on-prem SQL Server as any other flavor - It has near 100% compatibility with on-prem SQL. Different from the other two flavors, it can be deployed into a VNet. Can be reserved by paying upfront and get a hefty discount. The business model is also close to the on-prem one. The main differences of managed instance compared to other Azure SQL flavors:
- No active geo-replication
- SLA: 99.99% (in the middle of the range of other flavors)
- Supports built-in functions
- Runs CLR code
- No auto scaling & tuning. User has to take responsibility for them. 
- No availability zone
- No serverless tier
- No Hyperscale for superior performance

Use managed instance if you want your Azure SQL to be as close to the on-prem SQL Server as possible. 

Pricing (have two tiers - general purpose, and business critical): 
- General Purpose -> Instance Pools / Single Instance -> 8-80 vCores
- Business Critical -> Single Instance -> 8-80 vCores

## Cosmos DB

## Azure MySQL

## Azure PostgreSQL

## Azure Storage


## Redis Cache
Redis is used to store the Shopping Cart data for our ReadIt App.

## How to select data store solution
| Data Type | Used for | Examples | Options in Azure |
| ----------- | ----------- | ----------- | ----------- |
| Relational | Structured data | Items in store, demographic data | Azure SQL, MySQL, PostgreSQL
| NoSQL | Semi-structured data | Reviews, Log records, when flexibility is required | Cosmos DB (with SQL, Mongo, Azure Table demographic)
| Graph | Data representing relationships | Family tree | Cosmos DB (with Gremlin API)
| Blob | Files, videos, docs | Items' photos | Azure Blob Storage