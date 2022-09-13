# 10. Monitoring in Azure
We need to know the status of our app, how it performs, and when it has problems. This if done via monitoring. 

Azure offers a lot of build in monitoring mechanisms. There's a centralized monitoring hub where all monitoring data is streamed, and can be queried, or used for triggers. Monitoring in Azure is extremely cost effective. 

Monitoring is based on two types of data:
- `Metrics`. They are numeric values describing resource's aspect as a specific point in time. e.g.: CPU, Disk, Response Time
- `Logs`. Events that occurred in the system. e.g.: system started, error occurred

## Resource Monitoring
Almost every resource in Azure has a `Monitoring` section, including insights, alerts, metrics, logs, etc. 

## Dashboard
Dashboard can be customized, and its format can be exported as JSON file as a blueprint for creating other dashboards.

## Alerts
With alerts, you can get notifications about real time events in your resources, such as CPU usage exceeds 90%, etc.

Alerts components:
- `Condition` - when to trigger the alert
- `Actions` - What to do - usually send notifications
- `Details` - Contents of notification

Alerts pricing:
- Per metrics measured (0.1$/metric/month)
- Per notification type (1k emails, 1k push notifications, 100k web hooks, 100 sms in the US are included for free each month)

## Logs & Analytics Workspace
Almost every Azure resource generate logs, the logs might contain data about performance, events, errors etc. Log records need central repository to be stored and viewed, this is the `Log Analytics Workspace`:
- A `central loc` for storing, organizing and analyzing logs
- `Aggregates logs` from all connected, monitored resources
- Uses specialized query language to query logs Kusto query language (KQL). 
- Located in a `designated region`, which better be the same region of the resources to save costs. 

## Insights
`Insights` is a collection of metrics, statistics and insights about the resource that is specific to resource type. It is generated automatically. Code-based services (App Services, apps on VMs) can integrate Application Insights into the code and gain a lot of data about app usage, performance, etc. 

There's a lot more to Application Insights than availability tests. You can add the Application Insights SDK to your code (supported for .NET, Java, nodeJS & Python) and get a full, comprehensive monitoring solution for your app, which will help you detect:
- Request rates, response times and failure rates
- Dependency rates and failures
- Exceptions
- Page views and load performance

## Azure Monitor
A central loc for all the monitoring aspects of Azure's resources. It provides access to metrics, logs, insights, etc, and has additional capabilities not found in the individual resources. 

## Resource Tags
Resources are organized into Resource Groups, but sometimes we need more information about resources, like to which environment does the resource belong to (test/production); to which app it belongs; which group is rensponsible for it, etc.

`Tags` help organize resources usings name-value pairs. Tags can be set during creation of resource or after that. Tags allow us to show needed information in a simple query, it is also useful in cost analysis. 
 
Examples:
- Environment: Test
- App: ReadIt
- Group: the-team-A





















