# 5. Azure Compute
`Compute` is a set of cloud services for `hosting and running applications` - it allows uploading and running your code. It offeres various levels of control and flexibility. `Iaas` and `Paas` belong to the Compute topic. 

## 🏷 Virtual Machines
Virtual machine is a `virtual server` running on a `physical server`, this allows creating new servers extremely quick, because it is based on existing resources of the physical server. From the user's point of view, VM is a regular server. VM is an `unmanaged service`, because Azure doesn't manage what happens inside the VM - it is user's responsibility. VM is a classic `IaaS` service. 

### VM Architecture
<img src="images/img3.png">

VM density: num of VMs per host. 

### Create a VM in Azure
Select the location, the image (OS + Pre-Installed software), and the size. Always check the price first. 

Note that when you create a VM in Azure, many resources will be created at the same time, such as a `network security group`, a `public IP address`, `virtual network`, `network interface` and `the actual VM`. We can see these in the Deployment details section, but it doesn't show one more resource that is also created at the same time, which is the `disk`. This is important to know, because we pay some of these resources. 

Click "Go to resource", read through the Essentials section, and note that "Virtual network/subnet: first-vm-rg-vnet/default". Click the "Connect" button at the top bar. Usually, we connect to Windows VM using RDP, and Linux VM using RDH. Select RDP here, and in the new pane, click "Download RDP File". On a mac, could download free "Microsoft Remote Desktop" app from App Store, and open the RDP file with this app. The window will open and it looks like a actual Windows Server. Now close this window, and go to Overview page of the VM in the Portal and click "Stop" button at the top bar. Note that after stopping the VM, there are still payments that might inccur, mainly for storage and sometimes the IP address. In order to make sure that we will no longer pay for anything for the resources that we just created, we need to `delete the whole resource group`, that is why we create a new resource group when we create a new VM. So in the search bar, search Resource Groups, and see a list of all rgs in the accout. Click on the rg containing VM, and click "Delete resource group" in the top bar. 

### The Real Cost of VM
- The `VM` itself
- The `disk` of the VM
- `IP` - The public IP that is exposed by the VM, the price of the IP depends on the exact type of the IP that is exposed. Not all IP/public IP addresses has a cost. 
- `Storage` - Where the image of the VM is stored. It is stored in a storage account, and we have no access to it, but we need to pay for it. The price of the storage is extremely low. 

### Reducing the Cost of VM
- `Auto Shutdown` - Automatically shuts down the machine daily at the time you choose, mainly for test / dev machines. Note that storage and IP (if static) costs still incurred. Usually can save >50% of VM cost
- `Reserved Instances` - Allow upfront payment with substantial discount. It is usually offered for 1 or 3 years, and is great for production machine which run continuously. Offers great discounts (up to 62%), and can be divided to monthly payments. Cannot be stopped / refunded
- `Spot Instances` - Machines that run on unused capacity in Azure. Can be evicted any moment when needed by Azure. Offers up to 90% discount, price fluctuates according to demand. Great for non-critical, non-continuous tasks. ie. Batch processes, long running calculations
- `Disk Optimization` - Make sure to select the right disk for the machine. Default is `Premium SSD` – the most expensive option. Non IO-intensive machines can do with `Standard SSD`. ie. App servers, in-memory cache. Note: Disk type affects the `SLA`

More Cost Saving Techniques:

- Select the right size for your machine
- CPU shouldn’t rest, you pay for it...
- Select Linux over Windows when possible to avoid license fee
- Check price in nearby regions

### SLA of VM
`https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_9/`

Availability Concepts in Azure: Fault Domain, Update Domain, Availability Set, Availability Zone. 

`Fault Domain`: `Logical group of physical hardware` that share a `common power
source and network switch`. Similar to rack in a traditional data center. If there’s a problem with the power or networking in the domain (=rack) – all servers in it shut down. So you want to make sure your servers are spread
across more than one fault domain (=rack). 

`Update Domain`: `Logical group of physical hardware` that can `undergo maintenance and be rebooted at the same time`. Maintenance is done by Azure at its own discretion. If all your servers are in the same update domain, they’ll reboot at the same time during maintenance. You want to make sure your servers are spread across more than one update domain. 

`Availability Set`: `A collection of Fault Domains and Update Domains` your VMs will be spread across. It is always `in a same zone`. Can contain up to `3 Fault Domains` and up to `20 Update Domains`. `All domains (Fault & Update) are in the same Zone (=datacenter)`.

For example, if an availability set has 2 fault domains and 3 update domains, Azure will automatically spread your VMs in this availability set into separate fault and update domains. Without availability set, Azure might put these two VMs into the same fault domain, because it has no idea that these two VMs belong to the same application. 

Taking Advantage of Availability Set:

- Deploy identical VMs into t`he same Availability Set`, which ensures they won’t be shut down simultaneously when a single fault domain shuts down or an update domain reboots
- If needed – deploy `load balancer` to route between the VMs
- Availability Set is `free`, you pay only for the additional VMs

### Availability Zone
A `physically separate zone` within an `Azure region`. Technically – a building containing an autonomous data center. Provides protection against a `complete zone shutdown`. Hence the `better SLA`.

Taking Advantage of Availability Zone:

- Deploy identical VMs into `separate Availability Zones` in the same Region, which ensures they won’t be shut down simultaneously when the zone shuts down
- If needed – deploy `load balancer` to route between the VMs
- Availability Zone is `free`, you pay only for the additional VMs

Note that not all regions have availability zones. 

### VM Automation template (ARM template)
At the last stage of creating a VM in the portal, before clicking the "Create" button, click the "Download a template for automation" hyperlink. The Template page will open, and display the `ARM template (Azure Resource Manager Template)` of this VM, which is a JSON file describing the resources to be created. ARM template is used by Azure in (almost) all deployments, and can be exported, modified, uploaded, deployed; it can also be created from scratch. 

ARM Template is a `declarative` way of `deploying resources`. 

`Declarative` way describes the end result and allows “What-If” operation. It can deploy multiple resources at once. It can be integrated in CI/CD processes, and be source controlled. It is used ARM Template. 

`Imperative` way sends instructions to run. It is error prone, can’t be verified, and can’t be source controlled. It is suited for quick and dirty operations. It is used by Azure CLI, PowerShell (Although they can run ARM Template too). 

In the previous step, in the template page, it has a few tabs, including Template tab and the Parameters tab. If we click the Download button at the upper left, we will get the template as a zip file. Inside, there are two files: template.json and parameters.json. Now create a folder and copy these two files in. For the parameters.json file, you can change parameters such as osDiskType, and need to modify the value of the adminPassword field. Currently when deploy the template using the Portal, we cannot use the parameters file, it may change in the future. If we go to the Portal, and go to a list of resource groups, we can see "cloud-shell-storage-westus" rg which was automatically created when we use the cloud shell, and contains the storage account. Click on the only storage account inside this rg. In the overview page, click "File shares", and click on the single file share in the new page. Click "Add Directory", and name it "templates". Go into this dir so we can upload our templates file. Click "Upload", and select the template file and parameter file from local disk. Next, click the "Cloud Shell" button in the Portal, and use Bash. 
```
cd clouddrive
cd templates
dir
az deployment group create --resource-group [name-of-your-rg] --template-file template.json --parameters parameters.json
```















