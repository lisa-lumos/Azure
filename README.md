# Microsoft Azure project

## Redis Cache
Redis is used to store the Shopping Cart data for our ReadIt App.

## How to select data store solution
| Data Type | Used for | Examples | Options in Azure |
| ----------- | ----------- | ----------- | ----------- |
| Relational | Structured data | Items in store, demographic data | Azure SQL, MySQL, PostgreSQL
| NoSQL | Semi-structured data | Reviews, Log records, when flexibility is required | Cosmos DB (with SQL, Mongo, Azure Table demographic)
| Graph | Data representing relationships | Family tree | Cosmos DB (with Gremlin API)
| Blob | Files, videos, docs | Items' photos | Azure Blob Storage

## Messaging Services in Azure
Messaging is a core part of every Microservices architecture.

Four fully managed messaging services in Azure:
- **Storage Queue**: Part of the Azure Storage Account, pricing included in Storage Account, and same availability as Storage Account. The simplest queue implementation in Azure: Create queue -> Send message -> Receive message. Performance: for requests of 1kb msg - 20k msgs/sec/account or 2k msgs/sec/queue. Max msg size: 64kb. Has client libraries for many dev languages. Easy to learn.
- **Service Bus**:
- **Event Grid**:
- **Event Hubs**:
