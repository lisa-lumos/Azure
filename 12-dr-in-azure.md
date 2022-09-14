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










































