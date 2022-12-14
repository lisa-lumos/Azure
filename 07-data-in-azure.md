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
Cosmos DB is a fully managed NoSQL database with amazing performance - <10ms for 99% of operations. It is globally distributed, and has fully automatic management (updates, scaling, fixes etc). You can choose multiple APIs to interact with it (SQL, Mongo, Gremlin, Azure Table, Cassandra (per account)). 

Inside a Cosmos DB account, there are databases, with containers inside them. Inside containers, there are items that are JSON documents. 

### Cosmos DB Availability
It can be distributed across many regions (configurable), and the API automatically picks the closest one. When using write replication, SLA is 99.999%, which is the highest SLA in all Azure. All this availability are managed automatically, which no code changes required. 

### Cosmos DB Backup
Cosmos DB offers a full backup every 1-24 hrs (default is 4). The retention period is 20-30 days (default is 30). 

### Cosmos DB Security
IP firewall rules, Service Endpoints, Private Endpoints, Azure AD Authentication, Secure communication (TLS), Data encrypted by default

### Cosmos DB Partitions
Data items are divided to partitions (Logical group of items based on a specific property). E.g.: In a cars database, the Model can be a partition property. 

Partitions are the basic scale unit in Cosmos DB - Distribution and scale are per partitions. We need to make sure items are divided as evenly as possible. It’s extremely important to select the right partition property, because it cannot be modified later on. 

### Cosmos DB Consistency Levels
Traditionally, in relational db, we have strong consistency - the call returns only after successful commit in all replicas (High availability). While with NoSQL db, we have eventual consistency - The call returns immediately, while commit in replicas happens later (Low latency). Generally, high availability is not equal to low latency, and we have to select between them. 

But Cosmos DB offer 5 consistency levels:
- Strong - As in regular relational db
- Bounded Staleness
- Session
- Consistent Prefix
- Eventual - As in regular NoSQL DBs

The basic question with consistency is: if region A updates an item, and region B reads this item, which version will it get? 

With Strong consistency, region B will get the last version of the item updated in region X - the versions of the same item in both regions will always be identical. This consistency level is used for mission critical data when we must makes sure that the data is replicated immediately across regions, and no different versions of the same data exist in the system. 

With Bounded Staleness consistency level, region B will lag behind region A by k versions or t time. For example, the gap will never be larger than 3 versions. This consistency level keeps the order of the versions. It is used for low write latency and when order is important. 

With Session consistency level, in a client session, we have strong consistency, but for other clients, they will have the Consistent Prefix (sometimes Eventual) consistency level. 

The Consistent Prefix consistency level keeps the order of the versions, but there is no guarantee of the lag size (as opposed to Bounded Staleness). It is used for low write latency and when reads are infrequent. 

The Eventual consistency level provides no order guarantee, and no guarantee of the lag size. It can be used for the count of retweets, likes, etc, when there's no problem if the numbers fluctuate a bit. 

For Cosmos DB, the consistency levels can be configured at the account level, and can be relaxed on the request level. Not the word "Relaxed" - you cannot use a stronger consistency level at the request level, but you can use a weaker consistency level on the request level. There are no other dbs that offer such variety of consistency levels. 

### Cosmos DB Pricing
Based on Request Unit (RU) per Second. 1 RU = Read (get item by its ID, not by query) item of size 1kb. Note that update, delete, insert, query is going to cost more than 1 RU. You can see the actual RU consumed in the response header of the results. 

Beside RUs, the price is also based on operations type (Provisioned, Auto Scale, Serverless), write regions, num of provisioned RU/s. 

Database operations: 
- Provisioned - predefined num of RU/s, can be changed manually later. Offers reserved capacity of 65% discount. 
- Auto Scale - set the maximum RU/s, Cosmos scales up to this number. It is good for unpredictable loads. 
- Serverless - pay for what you use. In Preview currently, no SLA. 

### Creating and Using Cosmos DB
In the portal, search Cosmos. Create -> Select a API among SQL (recommended), MongoDB, Cassandra, Azure Table, Gremlin (here I choose sql) -> Create -> Resource Group: readit-app-rg, Account Name: readit-cosmos-db-lisa, Location: West Europe, Capacity mode: Provisioned throughput, Apply Free Tier Discount: Apply, Uncheck the checkbox for Limit the total amount of throughput that can be provisioned on this account, because it will cause a lot of problems in deployment. -> Go the the networking pane, Connectivity method (who can connect to it): All networks. -> Go to the Backup Policy pane, can see the default backup settings, which is 4 hr interval, with 8 hrs retention, and geo-redundant. -> Review+Create -> Create. 

Go to resource, in the Overview page, can see Read Locations and Write Locations. This is because you can have multi region deployment with Cosmos, which means data written in one region is also available for read at another region. There's a URI which will be used later. Go to the Firewall and virtual networks page under the Settings pane, we can define who can access cosmos db (default is All networks, means anyone can access). If you choose Selected networks, you can give an allowed IP address list that can access the cosmos db. 

By far, what we have created is an Azure Cosmos Account, not a cosmos db. Open Data Explore page -> New Container -> Database id: Create new & name it as readit-orders, Database throughput (autoscale): Manual, with 400RU/s, Container id: orders, Indexing: Automatic, Partition key: /priority, -> OK. 

Open the database readit-orders in the SQL API in the upper left corner, and can see a container named orders. To add items to the container, click New Item in the top bar, add JSON structure, and Save. Cosmos automatically adds a few key-val pairs to the item, such as _id. Repeat this to add another item. 

To query the db, click on the New SQL Query icon, and then you can query using sql syntax (although it is not a relational database). Such as:
```sql
select * from orders o; -- select all items

select * from orders o -- select all items x with x.items.name is 'Rama II'
where exists {
  select value n 
  from n in o.items
  where n.name = 'Rama II'
}
```

To edit the content (key or value or both) of an item in the UI, click on the item, modify the text and Update. Note that when we create the db, we used priority key as partition key. So we cannot delete this key-value pair. 

Go to Keys page under Settings pane, can see two keys and two connection strings. This allows for using the second key when we need to replace the first key, to avoid downtime. 

## Azure MySQL
Managed MySQL on Azure. Has great compatibility with on-prem MySQL database. Offers built-in security, backups, availability, etc. 

Security: IP firewall rules, Service endpoints, Private endpoints, Regular & Azure AD Authentication, Secure communication (TLS), Data encrypted by default. 

Backup (depends on Service Tier):
- Basic: full back up daily.
- General Purpose up to 4GB: full backup once a week, differential backup twice a day, transaction log backup: every 5min. 
- General Purpose up to 16GB: full backup once db is created, differential backup once a day, transaction log backup: every 5min. 

Retention Period: 7-35 days (7 is default). There is no native long term retention support as opposed to Azure SQL. 

Availability: For basically tiers, back up stored locally; for General Purpose and Memory Optimized tiers, backup is stored in a geo-redundantly. SLA: 99.99%

Pricing: 
- Tier: 
  - Basic - Require light computer and I/O performance (e.g.: for dev)
  - General Purpose - Most business workloads
  - Memory Optimized - for in-memory performance
- Compute power (num of vCores)
- Flexible Server deployment (in preview, not recommended)
- Reservations exist. 

### Creating and Using Azure MySQL
Search mysql in the portal. Create -> Single server, Create -> Resource group: mysql-rg, Server name: mytest-mysql, Region: West Europe, Version: 5.7, Compute _ storage: Configure server: Computer tier: Basic, Apply. Enter admin username and pwd -> Review + Create. 

Go to resource. Look at Overview page. Go to Connection security page, Add client IP, Save. Pricing tier page shows current pricing. Connection strings page. Could use MySQL workbench to connect to Azure MySQL (Hostname is Server name in the Overview page in Portal, Username is Server admin login name in Overview page).  

## Azure PostgreSQL
Managed. Create compatibility with on-prem PostgreSQL db. Includes Hyperscale deployment. Offers built-in security, backups, availability, etc. 

Security: IP firewall rules, Service endpoints, Private endpoints, Regular & Azure AD Authentication, Secure communication (TLS), Data encrypted by default. 

Backup (depends on storage size):
- Storage of 4GB: full backup once a week, differential backup twice a day, transaction log backup: every 5min. 
- Storage of 16GB: full backup once db is created, differential backup thrice a day, transaction log backup: every 5min. 

Retention Period: 7-35 days (7 is default). There is no native long term retention support for Azure PostgreSQL. 

Availability: For basically tiers, back up stored locally; for General Purpose and Memory Optimized tiers, backup is stored in a geo-redundantly. SLA: 99.99%

## Azure Storage
Object store; massively scalable; accessible via HTTP/HTTPS; has client libraries for almost every language. Durable and highly available. 

Azure storage types:
- Blobs (Binary Large Object): Object store, great for files, videos, documents, large texts, etc. Up to 4.77 TB per file, and 190 TB in preview. Extremely cost effective. Scalable. Availability options. Often used in conjunction with SQL/NoSQL database. 
- Files: File shares for cloud and on-prem deployments
- Queues
- Tables: NoSQL data store. Similar to CosmosDB, but less optimized for performance and availability, but cheaper. 
- Disks: Storage volumes for Azure VMs, managed by VM

Security: IP firewall rules, Service endpoints, Private endpoints, Shared Access Signatures, Access Keys & Azure AD Authentication, Secure communication (TLS), Data encrypted by default. 

Containers are logical groups of blobs. 

Azure Blobs has 6 options for storage redundancy. 
- Locally Redundant Storage (LRS). Data is synchronously copied 3 times within the same zone
- Zone Redundant Storage (ZRS). Data is synchronously copied to 3 zones in the Region. Only available to region with these zones. 
- Geo Redundant Storage (GRS). Data is synchronously copies 3 times within the same zone, then copied asynchronously to paired region. Data in the secondary region is accessible only after failover process
- Geo-Zone Redundant Storage (GZRS). Combination of GRS and ZRS. 
- Read Access - Geo Redundant Storage (RA-GRS). GRS with read-access data in the secondary region (without initiating failover)
- Read Access - Geo-Zone Redundant Storage (RA-GZRS).GZRS with read-access data in the secondary region (without initiating failover)

Azure Blobs Storage failover can be initiated via:
- Portal
- Azure CLI
- PowerShell

Blobs are uploaded to one of the 3 tiers:
- Hot. For data accessed frequently, has best SLA (99.9%, improves to 99.99% using RA-G(Z)RS), with highest storage costs and lowest access costs. E.g.: Photos to display, documents to show. 
- Cool. For data accessed infrequently, has lower SLA (99%, improves to 99.9% using RA-G(Z)RS), with lower storage costs and higher access costs. Must be stored for at least 30 days (or early deletion fees applied). E.g.: Short term backup, data for future processing. 
- Archive. Stored offline, no SLA - can take hours to retrieve. Has lowest storage costs and highest access costs. Must be stored for at least 180 days (or early deletion fees applied). Do not support ZRS, GRS, and RA-GRS redundancy. 

Note that tier is set a account level, but can be modified per blob. Moving between tiers can be automated by lifecycle rules. 





## Redis Cache
Redis is used to store the Shopping Cart data for our ReadIt App.

## How to select data store solution
| Data Type | Used for | Examples | Options in Azure |
| ----------- | ----------- | ----------- | ----------- |
| Relational | Structured data | Items in store, demographic data | Azure SQL, MySQL, PostgreSQL
| NoSQL | Semi-structured data | Reviews, Log records, when flexibility is required | Cosmos DB (with SQL, Mongo, Azure Table demographic)
| Graph | Data representing relationships | Family tree | Cosmos DB (with Gremlin API)
| Blob | Files, videos, docs | Items' photos | Azure Blob Storage