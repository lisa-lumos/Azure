# 8. Messaging in Azure
Messaging is a core part of every Microservices architecture.

Four fully managed messaging services in Azure: `Storage Queue, Service Bus, Event Grid, Event Hubs`. 

## Storage Queue
Part of the `Azure Storage Account`, pricing included in Storage Account, and same availability as Storage Account. The simplest queue implementation in Azure: `Create queue -> Send message -> Receive message`. 

Performance: for requests of 1kb msg - 20k msgs/sec/account or 2k msgs/sec/queue. Max msg size: 64kb. Has client libraries for many dev languages. Easy to learn.

### Using Storage Queue
Search storage accounts in the portal. Create -> Create a new rg, named queue-rg, Storage account name: storagequeue, Location: WE, Performance: Standard, Account kind: StorageV2, Replication: LRS -> Review + create. 

Go to resource, click the Queues rectangle, + Queue -> Queue name: store-messages -> OK. Click on this queue, + Add message -> Message text: ("id":4, "status":"success"), Expires in: 7 days, uncheck the Encode the message body in Base64, so we could see the content -> OK. To retrieve the message, in the c# code, put storage account connection string and queueName into the variables, and run the code. 

## Service Bus
Fully managed message queueing service. Durable - your chances of losing messages are extremely low. Supports point-to-point (Queue) and pub/sub (Topic) scenarios. Compatible with AMQP protocol (for IoT devices) and JMS 2.0 API (Java message services, Premium tier only).

Service bus queues: `Message sender` (web app, mobile app, service) -> `Service bus namespace` (contains a queue) -> `Message receiver` (Service / Application). Available in Basic Tier and above. 

Service bus topics: `Message sender` (web app, mobile app, service) -> `Service bus namespace` (contains a topic, which then goes into multiple subscriptions) -> `Message receiver` (business logic or audit , etc.).  Available in Standard Tier and above. 

Service Bus advanced features: 
- Message sessions (guarantees FIFO, aka first in first out of the queue)
- Dead-letter queue (holds messages that couldn't be sent)
- Scheduled delivery 
- Transactions (Useful with async messaging apps)
- Duplicate detection
- ...

Availability: SLA: 99.9%; Can be configured for automatic geo-disaster recovery. 

Security: IP Firewall rules, Service endpoints, Private endpoints. 

Tiers: Basic, Standard, Premium. 

Pricing: based on the tier and num of operations. 

### Using Service Bus
Search Service bus in the portal, + Create -> Resource group: (create a new rg sb-rg), Namespace name: sbdemo, Location: WE, Pricing tier: Basic -> Create. 

Go to resource, +Queue -> Name: my-items -> Create. Under Entities pane, click Queues, and see this newly created queue. To get the connection string, go to Shared access policies under Settings pane, click RootManageSharedAccessKey, and see the field Primary Connection String. A short code can be used to send a test message. 

To check messages in this queue, go to this queue, and see that there is an active message in the queue. To see the content of this message, go to Service Bus Explorer under the Settings pane, go to the Receive tab, click Receive button, double click the returned metadata line, then we can see the actual content of the message. 

## Event Grid
Allows building event-based architectures, publishes events to interested parties, and has `no queue nor order`; has `strong integration with many Azure services`, and is cost effective, simple pricing with no tiers. High availability is built in. `Event sources -> Event grid -> Event handlers`. 

Terminology: 
- `Event` (What happened, e.g.: storage blob added), 
- `Publisher` (Who created the event, e.g.: Microsoft), 
- `Event Source` (Where the event happened, e.g.: Storage account), 
- `Topic` (a logical group of related events, this is what subscribers listen to), 
- `Subscription` (Which events interest me, e.g.: Storage blob added),
- `Event handler` (Where the event is sent, e.g.: Azure function, event hubs). 

SLA: 99.99%. Max event size: 1mb. 

Performance: 10m events/sec, or 5k events/sec/topic. 

Latency: subsecond end-to-end latency in the 99th percentile. 

Pricing: Based on num of operations - first 100k operations/month are free. 

When using event grid, create a topic (system/custom). 

## Event Hubs
`Big data streaming platform` and event ingestion service. It is basically a `managed Kafka implementation`. Although Kafka is defined as a big data streaming platform, not as a messaging platform, many organizations use it as a messaging platform, because it is extremely robust and flexible. Can receive millions of events per second. 

`Event producers` -> `Event Hubs` (contains partitions) -> `Event receivers` (contains multiple consumer groups)

`Event producers` are components that generate events. Can be any one with the client/HTTP client. Uses simple connection and API. 

`Partition`: A partition contains a single event stream, like a single queue that guarantees the order. A single partition has limited availability, therefore it is better to spread messages across partitions to improve availability - but then the order is not guaranteed (order is guaranteed in a single partition only, not across partitions). `Max 32 partitions` on a single  event hubs. 

`Consumer group`: Is a logic group of receivers, but belong to the same application. Event receiving is done via AMQP protocol. 

SLA: 
- Basic and standard tier: 99.95%
- Dedicated: 99.99%

Throughput is measured in Throughput Units (TU). 1TU = 
- Ingress (Input) of 1mb/sec or 1k events/sec
- Egress (Output) of 2mb/sec or 4096 events/sec

These TUs are prepurchased, and billed by the hour. 

Pricing: based on tier, ingress and TU. 

### Using Event Hubs












## Selecting messaging solution

| Service | Used for | Guarantees order | Max msg size | And also...
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Storage Queue | Dead  simple queueing | Yes | 54kb | Extremely simple, no additional cost |
| Event Grid | Event driven architectures | No | 1mb | Great integration with other services |
| Service Bus | Advanced queueing solutions | Yes | 256kb | Advanced messaging features, durable |
| Event Hubs | Big data streaming | Yes | 1mb | Low latency, designed for heavy load |