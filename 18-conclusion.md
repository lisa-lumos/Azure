# 18. Conclusion

## Architecture Summary
When designing cloud architectures for Microsoft Azure, use this summary that will help you design the system select the right resources for the right task.

| Area | Resources / Notes |
| ----------- | ----------- |
| Compute  | <li>`Virtual Machines (VMs)` – When a full control is needed, or dev platform is not supported on any other cloud service (e.g. C++). Can be auto scaled using Scale Set.</li> <li>`App Service` – PaaS for web apps. Use when system is a web app running on modern platform. Not requiring any setup or configuration. Great integration with authentication providers and Application Gateway. <li>`Azure Kubernetes Services (AKS)` – If your apps already run on Kubernetes or Docker containers – use it. If you plan to deploy your app using containers – use it. <li>`Azure Functions` – Use for focused, lightweight actions (calculations, conversions, validation etc.). Extremely efficient, beware of cold starts.|
| Networking | <li>`VNet – Virtual Network`. By default inaccessible to other VNets. Design your app around VNets using the Hob-and-Spoke model. <li>`Subnet` – Logical segment inside a VNet. By default accessible from other subnets in the VNet. <li>`Network Security Group (NSG)` – Filters traffic based on 5 tuples. ALWAYS set up NSG in front of every subnet. <li>`Load Balancer` – Balances load across various resources based on various algorithms. Works on Layer 4. Use ONLY for internal resources. <li>`Application Gateway` – Used to expose web endpoint to the outside world. <li>`Load balancer`, `WAF` (optional), works on Layer 7. Use in front of App Services. |
| Data | <li>`Relational Databases` – Azure SQL (the only resource with 99.995% SLA), Azure MySQL, Azure Postgres. <li>`NoSQL Database` – Cosmos DB (the only resource with 99.999% SLA). <li>`Object Store` – Blob Storage|
| Messaging | <li>`Storage Queue` – Dead simple queueing, no additional cost, max msg size – 64KB. <li>`Event Grid` – Messaging system for Event Driven Architectures, great integration with other services, max msg size – 1MB. <li>`Service Bus` – Advanced queueing solution with advanced features, durable, max msg size – 256KB. <li>`Event Hubs` – Big data streaming, designed for heavy load, Kafka-based. Max msg size – 1MB |
| Authentication | <li>`Azure Active Directory` – Identity and Access Manager (IAM), extremely robust, supports MFA, conditional access and more. Great integration with App Services. <li>`Azure AD B2C` – Simplifies integration with apps for identity scenarios (log in, log out, sign up, etc)|
| Monitoring | <li>Set up `Alerts` to get notifications when something goes wrong. <li>Use `Metrics` to see system’s status. <li>Read `Logs` to find out what the system did. <li>Put important information on `Dashboards` to get wholistic view. <li>Use `Tags` to categorize the resources |
| Security | <li>Close unnecessary open ports of a VM <li>Use NSG <li>Use authentication <li>Encrypt data at rest and at transit <li>Use KeyVault to securely store secrets <li>Use Security Center regularly |
| DR | <li>Decide between hot and cold DR <li>Remember – hot DR is much more difficult to design and expensive to implement <li>Use Traffic Manager or Front Door for automatic routing |















