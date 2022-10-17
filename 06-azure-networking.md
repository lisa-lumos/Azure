# 6. Azure networking
Networking is the foundation of cloud security. Networking knowledge is what makes a good cloud architect. 

For example, in the VM section, if we allow a VM to be directly accessible from the internet, then there are two main threats: Brute force attacks on port 3389 (RDP), and No line of defense in front of the VM web server (which means when a user browses to this machine, there isn't any protection in front of the VM for external threats, which is usually the role of a firewall or application gateway)

## VNets (Virtual Networks)
VNet is a network in which you can deploy cloud resources (can think of as organization's private network). “Virtual” as in “based on physical network and logically separated from other virtual networks”. Many cloud resources are deployed within VNets, such as VMs, App Services, DBs, etc. Resources in a VNet can communicate with each other by default, but they cannot communicate with resources in other VNets by default. It's equivalent in AWS is called VPC (Virtual Private Cloud). 

VNets are free. But there's a 50 VNets per subscription across all regions. A VNet is scoped to a single region (cannot span multiple regions) and a single subscription (cannot share a VNet across multiple subscriptions). VNets can be connected to each other via Peering. VNets are segmented into Subnets, and are protected using NSG (network security group) defined on the subnets. 

The most important thing to think about when designing network: How to limit access to the resources in the VNet so that risk is minimized. 

Each VNets has its own address range (IP Range). By default, there are 65,536 addresses - this number can be customized. All network devices must be in this address range, expressed using CIDR Notation. 

### CIDR Notation


## SubNets

## Load Balancer

## Application Gateway






























