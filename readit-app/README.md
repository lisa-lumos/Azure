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

Create a new VM named "catalog-vm" via the portal, and place in a new rg called "readit-app-rg". Set the image of the VM as Windows Server Datacenter. Select size as Standard B2ms. And use standard SSD as OS Disk type. In the Networking tab, in the Virtual network field, createa new VN and name it as readit-app-vnet. In the Public IP field, by default Azure use dynamic IP so the IP changes each time the machine starts up, but we need it to be static, so click create new, and select "Static" instead of "Dynamic", and select Basic SKU. Next, go to Management tab, and Enable auto-shutdown. Then go ahead and create the VM. 

Connect to the VM using rdp, and wait for the Server Manager to load up, so we can configure the machine for easy web browsing. Go to Local Sever on the left pane, on the right, near "IE Enhanced Security Configuration", click the "On" hyperlink, in the pop up window, select "Off" for both Admins and Users, and save. Next, go to Dashboard in the left pane, and click "Add rules and features", In the new window, click next, next, next, then in the list, select "Web Server (IIS)" role, click "Add features", next, click next on Features page, at the Role Services page, select "Custom Logging" and "Logging Tools", click next, and Confirm by cliking Install. After done, close it.  

Open web browser, go to `localhost`, and we should see IIS Window Server welcome page, which confirms that a web server is installed on this machine. I prefer to use Google Chrome. Before we can install the catalog app, we need to prepare Windows Server for dotnet core apps, by installing dotnet core. Go to `dotnet/microsoft.com/en-us/download`, and click the "All .NET 6.0 downloads" hyperlink, then on the right side, click "Hosting Bundle" for Windows. Install it. 

Next, create a new folder, so the published files of the catalog can be copied to. Open File Explorer, go to the C drive, and create a new folder named "catalog". Now go back to your local machine, go inside the "publish" folder, and copy all contents inside the publish folder, and paste it into the newly created folder in the VM. If it crashes on mac, try to paste in batches. 

The next step is to tell the web server that the catalog folder is a web app. Click on the windows button on the desktop, and type iis and open the Internet Information Services (IIS) manager. In the left pane, click the arrow on left of catalog-vm and expand it. Right click on Sites, and click "Add Website...". In the new window, type "catalog" as Site name, and select the Physical path as `C:\catalog`. In the Binding field, change the Port to 8080, and click OK. In the right section, click "Browse *:8080 (http)" hyperlink, now we have the catalog app running in the VM:
<img src = "images/catalog.png">

Right now, the app is accessible from the local VM, but we need to make it to be accessible from all users across the web. First go to this VM Overview page in the Portal, and find its public IP address. After appending :8080 to this IP, we find that this site is not accessible from our local machine. Closing the firewall of the VM also will not help (not recommended). The reason is that, Azure newtworking features automatically blocks any external access to the VM, besides the rdp port..

If we go back the VM, and in the catalog app, click "Load Books to DB", we can see a list of books available. 

## Setting up the Weather API
In the VM, in the catalog app, on the top bar, we have a Weather API, for this feature to work, we will create another VM. Go to Azure Portal, and create a new VM. Use the same rg as the catalog app. VM named "weather-vm", region same. And this time choose the Image as Ubuntu Server. Choose a smaller size such as "B1s". Select Authentication type as "Password". Use Standard SSD as disk type. For networking, we need to make sure that this VM is in the same networks as the catalog app, so Azrue automatically set it as "readit-app-vnet", which is correct. Again, use static public IP for this VM. Also, in the Management tab, enable auto shutdown. 

After the VM is created, go to its overveiw page and find its "Public IP address", copy it, and go to Terminal in local mac, and type `ssh yourusername@vmIP`, such as `ssh lisalumos@130.1.3.78`. Note that the weather app is developed using node.js, not .net core. In the connection to remote machine, type `sudo apt install git`, `sudo apt update`, `sudo apt install nodejs`, `sudo git clone https://github.com/memilavi/weatherAPI.git`, `cd weatherAPI`, `sudo apt install npm`, `npm start`:
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

Note that is is a back practice to allow a VM to be directly accessible from the internet so it ican be rdped from anywhere. You should never leave a VM open to the internet this way. 








