# 15. Architecting Apps for Azure
Architecting for the cloud is different than classic Software Architecture. There are two main differences: Using existing services on the cloud, and always consider cost. Azure contains hundreds of services, they are usually managed, reliable and scalable, and cost effective. Cloud architecture is cost oriented, you will sometimes go for limited services due to cost reasons - for example, using Storage Queue vs Service Bus. 

## Choosing Compute Platform
<img src="images/img1.png">

## Choosing a Data Platform
| Data Type | Used For… | Examples | Options in Azure |
| ----------- | ----------- | ----------- | ----------- |
| Relational | Structured data | Items in store, demographic data| Azure SQL, MySQL, PostgreSQL |
| NoSQL | Semi-structured data | Reviews, Log records, when flexibility is required |  Cosmos DB (with SQL, Mongo, Azure Table API) |
| Graph | Data representing relationships | Family tree | Cosmos DB (with Gremlin API) |
| Blob | Files, videos, docs | photos | Azure Blob Storage |

## Choosing Messaging Platform
| Service | Used For… | Guarantees Order | Max Msg Size | And also… |
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Storage Queue  | Dead simple queueing | Yes| 64KB | Extremely simple, no additional cost|
| Event Grid | Event driven architectures | No |  1MB | Great integration with other services |
| Service Bus  | Advanced queueing solutions | Yes | 256KB | Advanced messaging features, durable |
| Event Hubs | Big data streaming | Yes | 1MB |Low latency, designed for heavy load |

## Implementing Security
Security is extremely important in the cloud, so you should use the best practices discussed in the Security section, mainly: 
- Restrict access to VMs and App Services
- Use NSG
- Use encryption in data stores
- Use strong authentication

## Implementing Logging and Monitoring
- Azure offers various logging and monitoring tools
- Utilize alerts to notify on any exceptional situation
- Create dashboards to visualize the system state
- Use Application Insight to gain insights into your app

## Azure Architecture Center
- Central hub for all-things Azure architecture
- How-tos, documents, design guidelines, case studies
- Fresh content, updated regularly
- url: `https://learn.microsoft.com/en-us/azure/architecture/`
















