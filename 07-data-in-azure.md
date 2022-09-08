# Data in Azure

## Redis Cache
Redis is used to store the Shopping Cart data for our ReadIt App.

## How to select data store solution
| Data Type | Used for | Examples | Options in Azure |
| ----------- | ----------- | ----------- | ----------- |
| Relational | Structured data | Items in store, demographic data | Azure SQL, MySQL, PostgreSQL
| NoSQL | Semi-structured data | Reviews, Log records, when flexibility is required | Cosmos DB (with SQL, Mongo, Azure Table demographic)
| Graph | Data representing relationships | Family tree | Cosmos DB (with Gremlin API)
| Blob | Files, videos, docs | Items' photos | Azure Blob Storage