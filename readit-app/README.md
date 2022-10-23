# The ReadIt App
The ReadIt App is a bookshop on the cloud, that simulates a full-blown real life bookshop. It contains 4 main services: 
- Books catalog - to pick the book you want to buy
- Shopping Cart
- Inventory Management
- Order Engine

The app starts from local and then is put to the cloud. The app is developed using .NET Core and nodeJS, and uses various databases. 

## .NET download
`https://dotnet.microsoft.com/en-us/download` to get .NET SDK 6.0 and .NET Core SDK (v3.1). Then open a new Terminal, and `dotnet --version` to check. Have problems with NuGet with M1 Mac, just ignore it. 

## VsCode and Packages
Install VSCode and the "C#", "Azure Account", "Azure App Service" package. 

## Running the Catalog Locally
Go to project folder "catalog", and `Run` -> `Start Debugging`. The catalog page will open in the browser. Note that there is no database attached to the project right now, and it is using memory.  

## Setting up the Catalog App
Open project folder in VS Code, and open its built in terminal, type `dotnet publish -o publish` and execute. There will be a new folder "publish" created in the project folder, its contents are the code to be published to the production server. 

Create a new VM named "catalog-vm" via the portal, and place in a new rg called "readit-app-rg". Set the image of the VM as Windows Server Datacenter. Select size as Standard B2ms. And use standard SSD as OS Disk type. In the Networking tab, in the Virtual network field, create a new VN and name it as readit-app-vnet. In the Public IP field, by default Azure use dynamic IP so the IP changes each time the machine starts up, but we need it to be static, so click create new, and select "Static" instead of "Dynamic", and select Basic SKU. Next, go to Management tab, and Enable auto-shutdown. Then go ahead and create the VM. 

Connect to the VM using rdp, and wait for the Server Manager to load up, so we can configure the machine for easy web browsing. Go to Local Sever on the left pane, on the right, near "IE Enhanced Security Configuration", click the "On" hyperlink, in the pop up window, select "Off" for both Admins and Users, and save. Next, go to Dashboard in the left pane, and click "Add rules and features", In the new window, click next, next, next, then in the list, select "Web Server (IIS)" role, click "Add features", next, click next on Features page, at the Role Services page, select "Custom Logging" and "Logging Tools", click next, and Confirm by clicking Install. After done, close it.  

Open web browser, go to `localhost`, and we should see IIS Window Server welcome page, which confirms that a web server is installed on this machine. I prefer to use Google Chrome. Before we can install the catalog app, we need to prepare Windows Server for dotnet core apps, by installing dotnet core. Go to `dotnet/microsoft.com/en-us/download`, and click the "All .NET 6.0 downloads" hyperlink, then on the right side, click "Hosting Bundle" for Windows. Install it. 

Next, create a new folder, so the published files of the catalog can be copied to. Open File Explorer, go to the C drive, and create a new folder named "catalog". Now go back to your local machine, go inside the "publish" folder, and copy all contents inside the publish folder, and paste it into the newly created folder in the VM. If it crashes on mac, try to paste in batches. 

The next step is to tell the web server that the catalog folder is a web app. Click on the windows button on the desktop, and type iis and open the Internet Information Services (IIS) manager. In the left pane, click the arrow on left of catalog-vm and expand it. Right click on Sites, and click "Add Website...". In the new window, type "catalog" as Site name, and select the Physical path as `C:\catalog`. In the Binding field, change the Port to 8080, and click OK. In the right section, click "Browse *:8080 (http)" hyperlink, now we have the catalog app running in the VM:
<img src = "images/catalog.png">

Right now, the app is accessible from the local VM, but we need to make it to be accessible from all users across the web. First go to this VM Overview page in the Portal, and find its public IP address. After appending :8080 to this IP, we find that this site is not accessible from our local machine. Closing the firewall of the VM also will not help (not recommended). The reason is that, Azure networking features automatically blocks any external access to the VM, besides the rdp port..

If we go back the VM, and in the catalog app, click "Load Books to DB", we can see a list of books available. 

## Setting up the Weather API
In the VM, in the catalog app, on the top bar, we have a Weather API, for this feature to work, we will create another VM. Go to Azure Portal, and create a new VM. Use the same rg as the catalog app. VM named "weather-vm", region same. And this time choose the Image as Ubuntu Server. Choose a smaller size such as "B1s". Select Authentication type as "Password". Use Standard SSD as disk type. For networking, we need to make sure that this VM is in the same networks as the catalog app, so Azure automatically set it as "readit-app-vnet", which is correct. Again, use static public IP for this VM. Also, in the Management tab, enable auto shutdown. 

After the VM is created, go to its overview page and find its "Public IP address", copy it, and go to Terminal in local mac, and type `ssh yourusername@vmIP`, such as `ssh lisalumos@130.1.3.78`. Note that the weather app is developed using node.js, not .net core. In the connection to remote machine, type `sudo apt install git`, `sudo apt update`, `sudo apt install nodejs`, `sudo git clone https://github.com/memilavi/weatherAPI.git`, `cd weatherAPI`, `sudo apt install npm`, `npm start`:
<img src = "images/weatherAPI_1.png">

Go to the VM overview page, and note down the Private IP address, it is located in the lower half of the screen, under Properties tab, in the Networking section. Next, in the catalog VM in the app, enter the private ip of weather VM, followed by :8080 port number, and click Get Weather: 
<img src = "images/weatherAPI_2.png">

Virtual Machines Tips and Tricks: 
- If your VM needs more disks, in addition to the default one provided by Azure, then go to the Disks page for that, and add whatever disk you need. Don't forget that disks have costs, and check it in the calculator beforehand.
- Want to backup your VM so that it can be restored in a case of failure? Check the Backup page, where you can define the frequency of the backup and the retention period.
- You can define DNS name for the VM, so that it will be accessible not just using its IP. This can be done by clicking the DNS Name: Configure link in the Overview page.

When not in use, we can stop the VMs so we no longer pay for it. But we still need to pay for the static public IP address. 

## Azure Architecture Diagram
When designing architecture for Azure apps it’s a good idea to use Azure symbols in the diagram, and there are hundreds of them: `https://docs.microsoft.com/en-us/azure/architecture/icons/`. 

Here is what current architecture look like:

<img src="images/architecture1.png" style="width: 30%">

Note that is is a back practice to allow a VM to be directly accessible from the internet so it can be rdped from anywhere. You should never leave a VM open to the internet this way. 

## Inventory App - App Service
Open the "inventory" folder in VScode, and hit F5 to start debugging, then will see the inventory app open in browser. 

To deploy this inventory app to app service, go to the portal, and search "app services", and "Create". In the new page, use "readit-app-rg" as the resource group. Unlike VM where we can pick any name we want, name of an app service is part of the app service domain itself (`.azurewebsites.net`), and has to be unique across all Azure. I name it "readit-inventory-lisa". Select Publish as Code, Runtime stack as .Net 6 (LTS), OS as Windows, and use the free F1 as size. And create. 

Go to the resource, and click the URL "https://readit-inventory-lisa.azurewebsites.net" in the overview page, can see the place holder page for the app service. Now go back to vscode, and stop running the code, and go to its built in terminal and `dotnet publish -o publish`. In the left pane in explorer view, right click the "publish" folder, and click "Deploy to Web App...". This option comes from the azure web service extension we installed earlier. Signin to Azure in the browser window that opens, and select the subscription, and click the app service "readit-inventory-lisa", and click Deploy. (For mac, need to go to vscode settings, and search Azure, and under Workspace pane, set the Deploy Subpath \ to /). Click Open the website, and will see the deployed inventory app:

<img src="images/inventory1.png">

To get a close look at this app service in Azure, go to the "Development Tools" section in the left pane, and click "App Service Editor (Preview)", and click "Go". This allows to edit and view the code itself. Click "Console" under the "Development Tools", which gives us a CLI into the machine that holds this app service. `dir` shows a list of the files that are deployed in the service. Under "App Service Plan" section, under the same name, shows the tier for the app service. The free tier is limited to `60 min of use per day`. The plan can be `scaled up`(manually) or `scaled out`(auto scaling, not supported for free tier) in the Settings section, and can select different plans under different tiers (dev/test, production, isolated). 

## Current Architecture
<img src="images/architecture2.png" style="width: 40%">

## Cart App
### Container part
Next step is to deploy the cart app into a container on Azure. Install Docker Desktop and Azure CLI using `brew update && brew install azure-cli` on the local machine. First run the app locally. Next install the Docker extension in VS code. Right click on the Dockerfile in the cart folder, and select "Build Image in Azure..."  (tag image as cart:latest and use linux platform). After build is complete, to run a new container based on this image, click the docker pane on the left bar, and under Images section, open the drop down menu near cart, and right click the "latest", and click Run. In the containers section, the container is called "cart:latest". Right click on it, and "Open in Browser" - we can see the app opens same as before. 

Next, we will create an Azure container registry, and upload the docker image to this registry. Under the Registries section, click on "Connect Registry", and select Azure. Right click on the "Azure subscription 1" in the Azure registry, and "Create Registry...", name it "readitacrlisa", and select the "Basic" tier, which will cost around 5$/month. Select resource group as "readit-app-rg", and location as "West Europe". If a message pops up as "The subscription is not registered to use namespace 'Microsoft.ContainerRegistry'", then we need to the Portal, and go to Subscriptions, then Resource providers, and search for "registry", and click "Register". Again sometimes it doesn't refresh the webpage even if it is registered, so could use `az provider show --namespace Microsoft.ContainerRegistry -o table` to check it using the cloud shell. To see the registry in the portal, search "container registries" in the search bar. 

To push the container app to Azure container registry, in  VS code, under the Images right click the "latest", and click "Push...", and select the newly created registry, and hit enter. After the push is complete, go to the portal and go to the Repositories page in the Services section, we can see the cart image. 

The next step is to create an AKS cluster and attach it to our newly created container registry, so it can pull the image from this registry. 

### AKS part
In the search bar in the Portal, search for "AKS", and click on "Kubernetes Services". Create -> Create a Kubernetes cluster. Choose the "readit-app-rg" as resource group, and the "Dev/Test($)" to save cost. For the cluster name, use "cart-aks", West Europe as Region, none availability zone, and choose "99.5%" for availability. 

When select the Node/VM size, we can see that many options shows Insufficient quota. This is because every subscription has a quota for maximum num of resources (all sorts of resources, such as num of VNs, num of vCPUs, etc) you can use. To see how many resources in the quota we have used, go to the subscription, and click on Usage + quotas in the Settings section in the left pane. Note that the number of vCPUs is in the quota is a regional number - since it is per region, we can use other regions if we reached quota for one region. We can see quotas in different regions using the filter bar on top. So select a different region for AKS, and use the D2ads_v5. Use the Manual Scale method, and use 1 as Node count. 

In the Integrations tab, and under Container registry, select the previously created registry. Review+Create -> Create. 

After the deployment is complete, go to the resource, and check the specs. 

Next step is to deploy the container into this AKS using VS code. Install the cli of AKS to we can run Kubernetes commands. In the terminal, type `sudo az aks install-cli`. Add below paths to ~/.bash_profile
```
export PATH=$PATH:/usr/local/bin/kubectl
export PATH=$PATH:/usr/local/bin
```
And `source ~/.bash_profile` in mac terminal. Test that `kubelogin` and `kubectl` commands can be found. 

Next, need to login to Azure from vscode terminal: `az login` and login from browser, then connect to the AKS cluster and get the credentials: `az aks get-credentials --resource-group readit-app-rg --name cart-aks`, and should see this returned: Merged "cart-aks" as current context in /Users/lisa/.kube/config. To see what is in the cluster: `kubectl get nodes`, then we should see:
```
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-17225725-vmss000000   Ready    agent   17m   v1.23.12
```

Next, open the deployment.yaml file in the cart folder. This file specifies how the cubectl to deploy containers to the Kubernetes. Change line #16 to the actual ACR for the app: `image: readitacrlisa.azurecr.io/cart:latest`. Now we are ready to deploy the container to kubernetes. In the vs code terminal, type `kubectl apply -f deployment.yaml`. The return should be:
```
deployment.apps/readit-cart created
service/readit-cart created
```

Portal -> cart-aks -> Services and ingresses under Kubernetes resources section in the left bar -> note the readit-cart service in the list. click on it -> Click on Pods in the bottom pane, it should show Ready 1/1. Now go to the external IP of the readit-cart:

<img src="images/cart.png">

## Current Architecture
<img src="images/architecture3.png" style="width: 60%">

## Running Functions Locally
Go to `https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cmacos%2Ccsharp%2Cportal%2Cbash` and install Azure Functions Core Tools locally. Open the order folder in VS code, the ProcessOrderStorage.cs receives order details from a http request and and store it into storage. While for ProcessOrderCosmos.cs, the order details is stored in the Cosmos DB. Install Azure functions extension in vs code. Run `dotnet restore` in Terminal in vs code. Run -> Start Debugging, now can see 
```
Functions:
        ProcessOrderCosmos: [POST] http://localhost:7071/api/ProcessOrderCosmos

        ProcessOrderStorage: [POST] http://localhost:7071/api/ProcessOrderStorage
```

To test the functions, open Postman, and paste content of "ordersample.json" file as raw format in the Body section. Send a  post request to `http://localhost:7071/api/ProcessOrderCosmos`, and will see the json content display in vs code terminal. 

## Running Functions in Azure
For trial accounts, you cannot create a consumption based function app using the portal, so open cloud shell, and create a storage account by `az storage account create --name readitfuncstoragelisa --location westeurope --resource-group readit-app-rg --sku Standard_LRS`. Next, create the function app by `az functionapp create --name readitfunctionapplisa --storage-account readitfuncstoragelisa --consumption-plan-location westeurope --resource-group readit-app-rg --functions-version 4`. Next, go to the Function App page in the Portal, and should see the app called "readitfunctionapplisa". 

Now click on this function, and see that the Functions page is empty. To load a function to this function app, go to the Azure tab in vs code, and under workspace, click the cloud icon, and select "Deploy to Function App..." and click the app, and select the function app name on azure. After deployment complete, click on Upload Settings. 

Now the two functions are in the function app in Azure. You can click on the first function and go to the Code + Test pane to test it. 

## Current architecture
<img src="images/architecture4.png" style="width: 70%">

## Readit VNet
If we look at the two VMs that we have deployed, notice that their subnet is called default in the readit-app-vnet VNet. Recall that the catalog vm could talk with the weather vm, this is because both machines are in the same VNet. Click on this VNet in the overview page of the VM, it will bring us to the overview page of this VNet. See the address space shows: 10.0.0.0/16, which means only first two numbers are fixed, and the latter two number can be used for network devices in this VNet. If we navigate to the Connected devices page, then we can see the IP address of the two VMs: 10.0.0.4 and 10.0.0.5. If navigate to the subnet page, will see that there's a single subnet called "default", with IP range of 10.0.0.0/24, which is a subset of the VNet it belongs to. 

The Address space of the VNet can be edited by going to the Address space pane of this VNet. Also, you can create new subnets in this VNet by going into the Subnets pane. Note that for each subset, Azure reserves 5 out of its range. 

To create a new VNet, search virtual network in the portal, and click create. If this is only for testing, you can put it into a new rg, so it is easy to delete later. During the creation of VNet, you can define a few subnets inside, with different names and ranges. 

## Set up NSG for the Catalog VM
Start this VM so could test it later. Go to the "Networking" pane, the list of Inbound port rules and Outbound port rules shows the NSG rules that are attached to this VM. It also shows that the NSG attached to this VM impacts 0 subnets, and 1 network interfaces (this VM). By default, Azure creates 4 rules in this NSG from slow priority to top priority: DenyAllInBound, AllowAzureLoadBalancerInBound, AllowVnetInBound (allows peering from other vnets in Azure), RDP (currently allows all traffic in). Click on the RDP rule to edit it. To get your current IP, just Google it. For the Source field, select IP Addresses, and in the Source IP addresses field, type your current IP. And click save. 

Recall that we cannot connect to the catalog VM from another machine. This is because there is no NSG rule that specifies allowing connection to 8080 port. To allow anyone (customers) to browse to this port, Click Add Inbound port rule, note that Azure automatically adds 8080 to the Destination port ranges field. Select only TCP as the Protocol, and add description in the Description field as "Allows browsing to the catalog app", and save. Now copy the public IP of this VM, and append :8080 to it, and open it in browser. Now we can see the catalog app from other than the VM. 

## Set up NSG for the Weather API
Perform the same steps for modifying the current SSH rule to it only accessible from your IP address. 

## Move the Weather API to a new subnet
Navigate to the weather-vm, and click on its VNet, and go to the subsets pane, and create a new Subnet. Name: weather-subnet, Network security group: leave it empty (Note in the dropdown menu, weather-vm-nsg is the one that we modified in the previous session. It was created automatically when this VM was created). Click save. 

The next step is to move the weather VM into this newly created subnet. Go to the Networking pane in the weather-vm, and click on the name of the Network Interface. Go to the IP configurations pane under Settings, and select the new subnet in the Subnet dropdown list. Note that the vm will restart if we change the subnet that it lives in. Click Save. 

To create a new nsg to attach to the new subnet, in the portal, search networks security group. Click Add. Resource group: readit-app-rg, Name: weather-subnet-nsg, Region: west europe. And create. Go to this resource, navigate to the Inbound security rules, see that there's no ssh rule attached to this nsg by default, this is because this nsg is not created during the creation of a linux vm. Navigate to the Subnets pane, click Associate, and select readit-app-vnet as the Virtual network, and then the weather-subnet as the Subnet. Hit Ok.  

Next, add SSH allowing nsg rule into this nsg, so we could ssh to the VM inside the subnet. This is because although the nsg of the vm allows ssh, but the nsg of the subnet that the vm lives in doesn't allow ssh. Click Add, Source: IP Address, Source IP addresses/CIDR ranges: (your IP), Destination port ranges: 22, Protocol: TCP, Name: SSH. Click Add. 

## Move the Weather API to a new VNet
Create a new VNet, note should not overlap with the address space of the current vnet. Go to Virtual Networks page in the Portal and click Create. Resource group: readit-app-rg, Name: weather-vnet, Region: west-europe. Go to the IP Addresses pane in the create page, and can see that the IPv4 address space that Azure shows there is not overlapped with the existing vnets. Modify the name of the default subnet to weather-subnet. Click Create. 

Next, need to move the weather API to this new VNet. Recall that to move the vm from one subnet to another, it was a lot easier - you just select this subnet. But moving a vm from one vnet to another is more complicated - we need to delete the weather VM and create it in the new vnet. 

Go to the weather vm, and click Delete. In the delete page, uncheck OS disk (so it can be used as the base for a new VM), check Network interfaces and Public IP addresses to delete both of them. Click Delete. 

In the portal, search disks, we can see the weather-vm disk1 has no Owner, while our catalog-vm disk is attached to the catalog-vm as Owner. Click on the weather-vm disk, and click Create VM. In the Create VM page, note that the Image field is already pre-populated with this disk. Virtual machine name: weather-vm, go to the Disks pane, note you cannot change OS disk type this time, because it already exists. In the Networking pane, Virtual network: weather-vnet, subnet: (defaulted to the only subnet in this vnet), Public IP: (note that catalog vm is connecting to weather vm using Azure network, not using the public IP. But we still need it to have a Public IP because we need to ssh to this machine to start the weather api) so create a public IP, and SKU: Basic, Assignment: Static. In the Management pane, Enable auto-shutdown: check, Notification before shutdown: uncheck. And Create. Note that Azure didn't ask us the credentials for this machine, this is because these credentials are already stored in the disk. 

Go to Resource, in the Overview page, see that the Virtual network/subnet: weather-vnet/weather-subnet. Go to the Networking pane, and modify the SSH rule to allow your IP only. Copy the public IP of this VM, and ssh to it and start the weather API. 

Now if we start the catalog vm and open the catalog app using its public IP, and go to the Weather page, and put in the weather vm private IP appending :8080, we cannot reach the weather API - because the two vms live in different vnets now. 

## Peering of the two vnets
Under the Networking pane of weather vm, click the hyperlink in network/subnet: weather-vnet/weather-subnet to go to the virtual network page. Go to the Peerings pane, click Add. This virtual network: Peering link name: readit-peering, Remote virtual network: Peering link name: weather-peering, Virtual network: readit-app-vnet. Click Add. Can see that Azure is adding two peerings, one from weather-vnet to readit-app-vnet, and one vise versa. 

Now if we start the catalog vm and open the catalog app using its public IP, and go to the Weather page, and put in the weather vm private IP appending :8080, we can reach the weather API. 

Just by defining the peering, the connection now works. We wonder why the NSGs did not block this connection. Go to the Networking pane of the weather vm, see that there is a rule called AllowVnetInBound - a default rule that allow traffic from VNets in Azure. 

As a test, to prevent anyone to use this VM at port 8080, click Add inbound port rule, Action: Deny. Click Save. Verify that is did prevent using weather API even from catalog VM. What we really want is to only allow traffic from this catalog vm, and block all the rest. Click on the rule we just created, and Source: IP Addresses, Source IP addresses/CIDR ranges: private IP of catalog vm, Action: Allow, Protocol: TCP, Description: Allow catalog VM to access the weather API. Hit Save. 




