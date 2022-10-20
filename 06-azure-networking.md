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



## 🏷 Load Balancer

## 🏷 Application Gateway






























