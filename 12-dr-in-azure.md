# 12. Disaster Recovery (DR) in Azure
`Disaster recovery` is `a plan to recover from a complete shutdown of a Region`, usually as a result of a disaster (earthquake, flood, etc.). Some apps require disastor recovery, some don't. DR might have substantial cost aspects. Remember: A complete shutdown of a Region is extremely rare. 

## How DR works
To set up DR, we need:
- Select a DR site, which is a secondary Region that will function as our primary in case of a disaster
- Configure it to be ready for activation when necessary

## DR Concepts
- `Hot DR`: failover to secondary site happens automatically with no downtime, no data loss. Requires duplicate infrastructure, and is most expensive (basically pay twice for everything). 
- `Cold DR`: failover to secondary site takes sometime, and might be manual. Some data might be lost, and it is less expensive because we don't need duplicate infra. 

How to decide hot or cold: For example, a global ecommerce website serving millions of customers is probabaly hot; while an HR app for the organization is definitely cold, if it has DR at all.

- `Recovery Point Objective (RPO)`: How much data we allow to lose in case of a disaster, in other words, what is the frequency of data sync to the secondary region. Usually measured in minutes. For example, 5 min.
- `Reovery Time Objective (RTO)`: How much downtime can tolerate in case of a disaster, in other words, how long it should take before the system is up again. Usually measured in minutes. When the system is back, it is not necessarily with the most up to date data - that depends on RPO. 

How to decide RPO and RTO: A massive reporting system will probably go for low RPO, but can compromise on th RTO, because it is important to have all the data, but the system doesn't need to be available all the time. A global chat will focus on RTO, because with chat, we need it to be available, but only care about current data, not hisotrical data. 

## Basics of DR Implementation
1. Restore the data (if the data is not synced in real time)
2. Activate the compute resources, if it is not already active
3. Modify routing (automatic or manual)

## DR of Data in Azure
If the RPO = 0, which means no data loss in case of disaster, then we need a database that always syncs (real time replication) with the secondary region. Currently, three such databases are offered in Azure:
- Azure SQL (with geo-replication and failover group)
- Cosmos DB (with multi-region account)
- Azure Storage (with GRS redundancy)

If the RPO > 0, which means some data can be lost in case of disaster, then we first need to ensure the database's backup freqency is compliant with the RPO, and that the backup storage should be GRS (geo redundancy storage). When DR happens, we use the data in the secondary storage account to restore to the new DB server instance in the secondary region. 

Note that the second case is much cheaper, because no secondary active database is needed when primary is active. 

## DR of Compute in Azure
If RTO = 0, which means there is no downtime in case of disaster, then the compute in secondary region should always be up and running. 

If RTO > 0, which means some downtime is tolerated in case of diaster, then we could have a non-active (passive) compute on standby in secondary region, or, we could create the compute in secondary region when disaster occurs. 

Note that the second case is much cheaper, because no secondary active compute is needed when primary is active. 

## Routing in DR
During DR, users should be routed to the secondary region. There a 3 major methods to achieve this:
- Inform the users about the new address of the app in the secondary region
- Manually change DNS record to point to the secondary region
- Use automatic routing. Azure has two services for this: Traffic Manager, and Front Door. 

### Azure Traffic Manager
Azure traffic manager is a DNS-based traffic load balancer. It enables traffic distribution across global Azure regions, and provides high availability and responsiveness. 

There are six routing algorithms used:
1. Priority - Always use primary service, when it's down, then use backup endpoints (DR)
2. Weighted - Distribute traffic across endpoints according to weights defined by you
3. Performance - Uses the closest region to improve latency
4. Geographic - Direct traffic to specific geography based on the loc of the DNS query
5. Multivalue - Returns list of healthy endpoints so the client can choose what to do with them
6. Subnet - map source IP to endpoint

Among all 6 of them, the first one gives the DR capability. Traffic Manager is extremely cheap priced queries in millions. 

### Azure Front Door 
Azure Front Door is Azure's global entry point for web apps. It works on layer 7 (http/https), and has multiple routing methods. It is similar to Application Gateway but in global scale. It has features such as URL-path based routing, Session affinity, SSL Offloading, Web Application Firewall (WAF) integration, URL rewrites, HTTP/2 support, etc. It's pricing is measured by inbound and outbound traffic. The price is not too high. 

### Traffic Manager vs Front Door
Generally, if you need http related capabilities go with Front Door. Otherwise, go with Traffic Manager, because it is usually cheaper. However, Front Door switches quickly to backend changes. 




























