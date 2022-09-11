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
- Condition - when to trigger the alert
- Actions - What to do - usually send notifications
- Details - Contents of notification

Alerts pricing:
- Per metrics measured (0.1$/metric/month)
- Per notification type (1k emails, 1k push notifications, 100k web hooks, 100 sms in the US are included for free each month)
























