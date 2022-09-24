# 2. Introduction to Azure

## Regions
Microsoft built a lot of datacenters for Azure, each datacenters’ `location` is called `Region`. There are ~60 Azure Regions (more than any other cloud). Almost every new resource in the cloud should be allocated to a region. 

## Zones
Some of the regions have more than one `physical datacenter`, Each datacenter is called a `zone`. When there are `more than one datacenter in a region`, the region is said to have `Availability Zones`. This is great for `availability` in case one datacenter fails - some cloud services benefit from Availability Zones. 

## Paired Regions
Some regions have `designated pair region` for increased `availability`. So when a full region fails – the other one can fill its place. This is relevant for some of the cloud services. `Pairs are set by Azure and cannot be changed`.

## Azure Services
Everything that can be done in the cloud is called a `Cloud Service`. ie. Creating VMs, building databases, set up networks, use AI algorithms, using central user management etc. This is a list of hundreds of the Azure Services: `https://azure.microsoft.com/en-us/services/`. 























