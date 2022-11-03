# 6. Azure networking
Networking is the foundation of cloud security. Networking knowledge is what makes a good cloud architect. 

For example, in the VM section, if we allow a VM to be directly accessible from the internet, then there are two main threats: Brute force attacks on port 3389 (RDP), and No line of defense in front of the VM web server (which means when a user browses to this machine, there isn't any protection in front of the VM for external threats, which is usually the role of a firewall or application gateway)

## 🏷 VNets (Virtual Networks)
VNet is a network in which you can deploy cloud resources (can think of as organization's private network). “Virtual” as in “based on physical network and logically separated from other virtual networks”. Many cloud resources are deployed within VNets, such as VMs, App Services, DBs, etc. Resources in a VNet can communicate with each other by default, but they cannot communicate with resources in other VNets by default. It's equivalent in AWS is called VPC (Virtual Private Cloud). 

VNets are free. But there's a 50 VNets per subscription across all regions. A VNet is scoped to a single region (cannot span multiple regions) and a single subscription (cannot share a VNet across multiple subscriptions). VNets can be connected to each other via Peering. VNets are segmented into Subnets, and are protected using NSG (network security group) defined on the subnets. 

The most important thing to think about when designing network: How to limit access to the resources in the VNet so that risk is minimized. 

Each VNets has its own address range (IP Range). By default, there are 65,536 addresses - this number can be customized. All network devices must be in this address range, expressed using CIDR Notation. 

### CIDR (Classless Inter-Domain Routing) Notation
It is a method for representing an IP Range, it is composed of an address in the range and a number between 0 and 32, which indicates the num of bits that are allocated to the address - the smaller the number, the larger the range. 

For example, 109.186.149.240, each loc is represented by 8 bits (0-255), then after it, 109.186.149.240/24, the 24 is the range, which means 24/8 = 3 locs are allocated to the address (fixed), and 8 bits (last loc) is allocated for the range, therefore the range goes from 109.186.149.0 to 109.186.149.255, which contains 256 addresses. There are a lot of CIDR calculators - ie. `https://www.ipaddressguide.com/cidr`. Also, Azure usually shows the actual range (start, end and the size of the range). 

## 🏷 SubNets
A subnet is a logical segment in the VNet that has a subset of the VNet’s IP Range. It is used as a logical group of resources in the VNet. Subnet is a must - Resources must be placed in a Subnet, cannot be placed directly in the VNet. By default, resources in a subnet can talk to resources in other subnets in
the same VNet, it can be customized. 

Each Subnet gets a share of the parent VNet’s IP Range. You should NEVER use the full range of the VNet in a Subnet, otherwise it would be extremely hard to modify this subnet range later, which then makes it hard to add future Subnets. 

For example, you can create two VMs under different subnets in one VNet, and then you can rdp within one VM to another VM using the private IP address of the second VM. 

### Network Security Group (NSG)
It is a gatekeeper for Subnets and defines who can connect in and out of subnet. It can be thought of as a mini-firewall. NSG should be a standard part of Subnet creation. NSG is free. 

NSG looks at 5 tuples: Source (Where did the connection come from), Source Port (The port the source is using), Destination (Where does the connection request goes), Destination Port (To which port does it want to connect), Protocol (TCP, UDP, Both). Based on these 5 tuples the connection is either allowed or denied (called Security Rule). Each rule is assigned a number, the lower the number – the higher the priority of the rule. 

An NSG is automatically created and attached to every newly created VM’s network interface. By default, open RDP (on Windows) or SSH (on Linux) port to
anyone, and this MUST be handled first thing after creation. 

NSG tip: In addition to setting up security rule per IP (as we did in the demo), you can also set up rules that use `Service Tags`. These service tags represent well known sources which can be used in the rule, and you don't have to know their IP address. Common Service Tags are:
- Internet: All the requests coming from the internet
- VirtualNetwork: Requests coming from other Azure's Virtual Networks (this service tag is used by the default rule created for every NSG)
- LoadBalancer: Requests coming from Azure Load Balancer (this service tag is used by the default rule created for every NSG)
- AzureMonitor: Requests coming from Azure Monitor, and are used for monitoring the app
- ...

It's recommended to use Service Tags when your rule applies to built-in resources, and not resources created by you.

### Network Peering
Sometimes, to increase security, we want to place some resources in a completely different VNet, not just different subnets. e.g.: 
- Separate systems
- System layers (front end and back end)
- Sensitive databases

The main reason is that we should not place non-public resources in a VNet that has public access. Doing this avoids the case when the user finds a way to bypass the front end vm to reach to the database directly. 

Network Peering allows two VNets to connect to each other. From the user’s point of view it’s a single VNet. To make peering possible, we should make sure address spaces are not overlapped. And we should always use NSG for protection. Peering can work across Regions. And it is not free (pay by the traffic volume in GB for both inbound and outbound). 

### Network Topology
With growth of system, it is hard to remember which resource lives in which subnet and which VNet, `Network Watcher` comes to rescue. Search for it in the portal, and in the Topology pane, Resource Group: readit-app-rg, it will show the current topology of our app. Another useful thing in the Network Watcher is the `Connection troubleshoot` pane - it allows you to check whether two resources in Azure could connect to each other, if cannot, why the connection is blocked. 

### Secure VM Access
Public APIs are considered attack surface, so hackers can hack into the VMs and cause data breach or shutdown the entire system. 

The larger the attack surface, the greater the risk. Having multiple public IPs exposed to the network increases the security risk, so we want to minimize it as much as possible. Always try to avoid leaving public IPs open, although we cannot always avoid it. To minimize the risk of public IPs being exposed to the internet, there are 4 techniques:
- JIT Access (Just In Time Access): Opens the port for access on demand, and automatically closes it when not using. Can be configured from the VM’s Configuration pane in the portal. Requires Azure Security Center License upgrade
- VPN: A secure tunnel to the VNet. Can be configured so that no one else can connect to the VNet. Requires VPN software and license (not part of Azure), so it is often complicated an expensive. 
- Jump Box: Place another VM in the same VNet, and allow access ONLY to this VNet. When need to access one of the other VMs – connect to this one first, and connect from it to the relevant VM. Only one port is open (still kind of a problem...). Cost: The additional VM (the Jump Box)
- Bastion: A web-based connection to the VM. No open port is required. Simple and secure. Cost: ~140$/month

#### Bastion
Bastion downsides:
- Cost
- Requires portal access to access the VM (as of year 2022), while rdp/ssh do not require portal access.

### Service Endpoint and Private Link (from VNet to a managed service)
Problem: A lot of managed services (Azure SQL Server, App Services, Storage, etc) expose public IP . Sometimes they are accessed only from resources in the cloud (e.g.: Database in the backend). They might pose a security risk.

This problem can be solved by Service Endpoint (it is a legacy solution) - they create a route from the source VNet to the managed service, so the traffic never leaves Azure backbone, although the service still has a public IP (access from the internet can be blocked). They are extremely difficult to use from on-prem networks. Service Endpoint is free. Resources support Service Endpoint: Storage, SQL Database, Synapse Analytics, PostgreSQL, MySQL, Cosmos DB, KeyVault, Service Bus, Event Hub, App Service, Cognitive Services. 

Steps to use Service Endpoint:
- Enable Service Endpoint on the source Subnet
- On the target resource, set the subnet as the source of traffic

Private link is a better and newer solution to the problem - it extends the managed service to the source VNet, so traffic never leaves the VNet and the resources talks via private IP, and hence access from the internet will be blocked. Private Link can be used from on-prem networks. Private Link is not free. Resources support Private Link (much longer than service endpoint's): Storage, SQL Database, Synapse Analytics, PostgreSQL, MySQL, Cosmos DB, KeyVault, Redis, AKS, Search, ACR, App Configuration, Backup, Service Bus, Event Hub, Monitor, Relay, Event Grid, App Service, Machine Learning, Automation, IOT Hub, SignalR, Batch. 

Steps to use Private Link:
- Configure the resource to connect to the VNet. 
- Configure private DNS, which handles the routing between resources. It is done automatically by Azure. Might cause a problem if you have your own DNS. 

Service Endpoint VS Private Link:
|  | Service Endpoint | Private Link |
| ----------- | ----------- | ----------- |
| Security | Connects via Public IP | Connects via Private IP |
| Simplicity | Very simple | More complex |
| Price | Free | Not free | 
| Supported services | Limited list | Large list, probably will get larger |
| On-Prem connectivity | Quite complex | Supported | 

### App Service VNet Integration (from app service to something inside VNet)
Allows access from App Service to resources within VNet, so that these resources should not be exposed on the internet. Extremely useful when App Service needs access to a VM with some internal resources. Supports same-region VNets. For VNets in other regions – a gateway is required. 

It is not available in free tier App Service Plan - needs standard tier. Navigate to the Scale up (App Service plane) pane, and click Production, then See additional options, choose S1 production tier. Go to the Networking pane, inside the Outbound Traffic box, click on VNet integration. Click Add VNet, and Virtual Network: readit-app-vnet, Subnet: (Can only connect to an empty subnet) AzureBastionSubnet. Could click OK to complete. After this test, revert back to F1 tier so it is cheaper. 

### App Service Access Restrictions
Similar to NSG – but for App Services. Restricts traffic to the App Service. By default, all inbound traffic is allowed (in relevant ports 80 and 443), but with access restrictions, inbound traffic is restricted to the allowed IPs / VNets / Service Tag (such as load balancer, which is commonly used). 

Main use cases:
- Backend App Service that should be accessed from front end App Service / VM only
- App Service that sits behind Application Gateway / Load Balancer, and shouldn’t be accessible directly
- Open App Service to a specific customer only (less common)

Steps: Go to the Networking pane of the app service, inside the Inbound Traffic box, click on Access restrictions. There's a single default rule sitting in there, which allows all traffic. (To add a rule that only allow for your IP address), Click Add rule, Name: my-ip, Action: Allow, Priority: 100, Description: Allow access from my IP, Type: IPv4, IP Address Block: (your IP), click Add rule. Note that a new rule is created automatically to deny all accesses, with a lower priority. Now other IP addresses trying to access the app service will get "403 forbidden" error. 

### ASE (App Service Environment)
Special type of app service deployed directly to a dedicated VNet. This VNet can be configured like any other VNet – Subnets, NSGs, etc. It is created on dedicated hardware, and is quite expensive.

Major use cases:
- Elevated security – complete isolation
- Very high scale requirements

To access this service, go to the app service you created before, and under Scale up (App Service plan) pane, select the Isolated (Advanced networking and scale) tab. 

## 🏷 Load Balancer
Azure service that distributes load and checks health of VMs (When a VM is not healthy, no traffic is directed to it). It can work with VMs or Scale Set. Can be public (be accessible from the internet) or private (used only in a closed VNet). It operates at layer 4 (Transport layer, which is related to IP, Port, protocol, TLS, etc, has no knowledge about the actual content)of the OSI model which separates the connection components into different layers: 

<img src="images/osi.png" styles="width: 50%">

Ref: `https://learn.microsoft.com/en-us/windows-hardware/drivers/network/windows-network-architecture-and-the-osi-model`. 

Load Balancer distribution algorithm is based on 5 tuple hash like those used in NSG: Source IP, Source port, Destination IP, Destination port, Protocol type.

Load Balancer types:
| Basic | Standard |
| ----------- | ----------- |
| No redundancy | Redundant |
| Open by default | Secure by default |
| Up to 300 instances  | Up to 1000 instances |
| No SLA  | 99.99% SLA |
| Free | Not Free |

There are 4 main configurations for Load Balancer: Frontend IP configuration (public IPs exposed by the Load Balancer), Backend pools (the VMs connected to the Load Balancer), Health probes (checks the health of the VMs, run in intervals of  a few configurable seconds; can run on TCP, HTTP, HTTPS (standard only); can configure unhealthy threshold - how many times a check should fail to mark the VM as Down, default is 2; ), Load balancing rules (A rule connecting Frontend IP with Backend pool). 

Health Probes runs on the VM's host, so no network traffic outside the host. The probes originate from the same IP: 168.63.129.16, and is allowed by default in NSG (the rule AllowAzureLoadBalancerinBound). 

Load Balancer is great for internal resources, but do not use it for external resources (Especially on Web Apps / Web API / etc. For this we have the Application Gateway) Because it can’t handle HTTP, and doesn’t route based on path. Also, there is no protection for them. 

## 🏷 Application Gateway
It is a web traffic load balancer - can function as the external endpoint of the web app. It Works with: VMs, VM Scale Sets, App Services, Kubernetes (requires some extra work). It is similar to the Load Balancer, but has additional features, such as SSL Termination, Autoscaling, Zone redundancy, Session affinity, URL based routing (very important! Not in load balancer), WebSocket and HTTP/2 support, Custom error pages, Header & URL rewrite, WAF, etc. It operates at layer 7 (Application layer) of the OSI model. 

One of the most important capabilities of the Application Gateway is the Web Application Firewall (WAF). It protects web apps against common attacks such as Cross-site scripting, SQL injection, etc. Protection rules are based on OWASP Core Rule Set, and updates continuously. WAF works in Detection (notify only) or Prevention (block and notify) mode. Many organizations have their own WAF deployment, usually based on 3rd party products (Palo Alto, Fortinet, Imperva etc.) In these cases – there’s no need for the WAF in the Application Gateway (only need to use it as is).

Application Gateway comes with two flavors: 
- Standard_V2 – includes all the features mentioned, excluding WAF
- WAF_V2 – Includes everything (almost double the price…)

Application Gateway is placed in its own Subnet, and often in its own VNet. Therefore, we must make sure the backend resources are accessible from the Application Gateway Subnet, but not accessible from anywhere else. For VM sitting in another VNet, this means modifying the NSG rules. And for App Service, it means using Service Endpoint + Access Restrictions, or Private Link. 

Application Gateway has 5 main configurations: Backend pools (The VMs, Scale Sets, or App Services connected to the Application Gateway), HTTP settings (Settings for the incoming HTTP requests, not there in load balancer), Frontend IP configurations (The public IP exposed by the Application Gateway), Listeners (Receives requests on a specific port and protocol), Rules (a rule connecting Listener with a Backend pool). 

Expect the cost to be ~$200/month at least. 



















