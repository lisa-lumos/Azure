# 3. First Look at Azure

## The Azure Portal
`https://portal.azure.com/`. The `Azure portal` is a web-based, unified console that provides an alternative to command-line tools. The hamberger button on the upper left corner shows all the services provided by Azure, however, the `Search Bar` at the top is most commonly used. You can run commands using `Azure Cloud Shell` (Bash or PowerShell). 

## Subscription and Account
`Subscription` is a logical container for resources, while `account` is an identity with which you access resources in the subscription. A Subscription contains the various resources you provision in the cloud (VMs, DBs, networks etc. ). Every resource will be contained/created in a subscription. A subscription can be attached to multiple accounts. An account can also be attached to multiple subscriptions.  

## Subscriptions
In Azure Portal, click the `Subscriptions` icon, this will show the list of subscriptions that we have access to. Currently, there is only one subscription "Azure subscription 1", whose name can be changed. Thsi subscription was created automatically when we crate an account, and Azure attached the account to this subscription. 

## Creating Resource
Go the the search bar, and type `resource groups`, click on this service. Click the + button to create it. Resource group is `free` to create. First, when we create a new resource, we need to assign it to a `subscription`, currently we only have one subscription. Next we could `name` the resource group as "my-rg". Almost every resource needs to belong to a specific `region`. When we click the dropdown of the Region field, we can see a list of regions that support Resource Groups. In this case, because resource group is so important, it is supported in every region. I will select "Central US" as my region. Next, click "Review + create", check the summary, and click "Create". Now you can see "my-rg" show up in Resource groups list. 

Every time you create a new resource, you need to pay attention to `notifications` in the top bar. Resource group takes minimal time to create, but other resources can take time to create, some even take 15 mins. 

Click on "my-rg" to see details inside. 

## Finding the Resource
At the `search bar` in the home page, search the resource you need. The pane also shows `recent resources`. Pay attention to the icon of different resources. 

## The Resource Page
When open the "my-rg" resource, the name and type of the resource is shown in the upper left corner. The left pane shows a list of actions you can perform on the resource, and the first one in the list is `Overview`, which is the default view for the resource page, it shows subscription, subscription id, location, etc. 

## Removing a Resource
In the `Overview` screen, there is a button called `Delete resource group`, 

## Azure CLI & PowerShell
`Azure Cloud Shell` allows us to use `Command Line Interface (CLI)` to run commands against Azure. Note that we don't have to use Azure Portal to run the commands, we can also download the Azure CLI and Azure PowerShell. One advantage of Auzre CLI (vs Azure PowerShell) is that you can `integrate` it into other applications you write, such as Python or Java apps. 

Open Azure Cloud Shell from the Portal, and click `bash`, select the subscription in which to create storage, proceed and wait for some seconds. In the next window that shows up, the upper left corner tells us that we are currently in the Bash shell. Type:
```
az group create -l westus -n CLITest-rg
```
And the return looks like:
```
{
  "id": "/subscriptions/[your id number ...]/resourceGroups/CLITest-rg",
  "location": "westus",
  "managedBy": null,
  "name": "CLITest-rg",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}
```
Now swith the Bash shell to PowerShell with the tab in the upper left corner, and type:
```
New-AzResourceGroup -Name PSTest-rg -Location westus
```
And the return looks like:
```
ResourceGroupName : PSTest-rg
Location          : westus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/[your id number ...]/resourceGroups/PSTest-rg
```
















