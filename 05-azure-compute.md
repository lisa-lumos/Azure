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
- The VM itself
- The disk of the VM
- IP - The public IP that is exposed by the VM, the price of the IP depends on the exact type of the IP that is exposed. Not all IP/public IP addresses has a cost. 
- Storage - Where the image of the VM is stored. It is stored in a storage account, and we have no access to it, but we need to pay for it. The price of the storage is extremely low. 



















