# Data in Azure
Azure provides many data solutions as cloud services, such as Relational databases, NoSQL databases, object stores. They are all fully managed services, and can be part of Azure app or completely independent. They have various pricing models, and are always better than unmanaged solutions, because you do not need to control the vm that holds the database, and therefore not responsible for patching, maintenance, etc. 

What to look for when selecting a database:
- Security: Network isolation, Encryption
- Backup: Backup types, Retention Period
- Availability: SLA, Replication, DR

Note that we always have the choice to install database on a VM (Azure VMs can be setup with database software). There are also ready-made VMs in the marketplace that comes with pre-installed databases, such as Oracle Database. The advantage of having database on VM is that you will have full flexibility and control to do whatever you wish. The disadvantage is that you need to take care of everything, such as SLA, updates, availability, security, backups, etc.  

## Azure SQL

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