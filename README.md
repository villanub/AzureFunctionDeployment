# Deploy an Azure Function using an ARM template

The SplunkVS branch contains a working version of the deployment template, tailored for a real version of a function that transmits Azure Monitor Logs to Splunk's HEC port. That function is [Azure Function For Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS)  


[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fvillanub%2FAzureFunctionDeployment%2FSplunkVS%2FazureDeploy.json)  

## Overview

The steps to fully implement the Azure Function for Splunk are:  
* In Azure, configure the diagnostics profiles of the resources that you want to monitor.
* In Splunk, switch on the HEC port and get the token value.
* Gather the settings below.
* Click the "Deploy to Azure" button below.
* Authenticate to the Azure Portal (if necessary)
* Fill in the form with the setting values
* Wait a few minutes for the function to be created and deployed
* In the Splunk UI, watch for events to appear.

The Activity Log does not generate massive amounts of events for most users. You can see the list of event types [here](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-activity-log-schema).

A new feature of Azure Monitor allows you to route all diagnostic log messages to a single event hub. See this [article](https://azure.microsoft.com/en-us/blog/azure-monitor-send-monitoring-data-to-an-event-hub/) for details.

## Settings

* AppName                     - this is the name of the function app. In the Azure Portal, this is the name that will appear in the list of resources.
* repoURL                     - this is the URL of the repo that contains the function app source.  
   Example: ```https://github.com/microsoft/AzureFunctionForSplunkVS```
* repoBranch                  - this is the name of the branch containing the code you want to deploy.  
   Example: ```master```
* eventHubConnectionString    - this is the SAS Policy connection string to your event hub namespace policy.  
   Example: ```Endpoint=sb://yournamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourkey```
   See screenshots showing how to get this value below.
* inputHubNameActivityLogs    - name of the hub (within the hub namespace) that receives Activity Log events  
   Example: ```insights-operational-logs```
   (at present, this is the only correct value. It becomes configurable in future.)
* inputHubNameDiagnosticsLogs - name of the hub that receives your diagnostic log events. You decide this name and configure Azure accordingly.  
   Example: ```insights-logs-diagnostics```
* inputHubNameMetrics - name of the hub that receives your Azure Monitor Metrics. You decide this name and configure Azure accordingly.  
   Example: ```insights-metrics-pt1m```
* inputHubNameWAD - name of the hub that receives telemetry from your configured Windows VMs. You decide this name and configure Azure accordingly.  
   Example: ```insights-telemetry-wad```
* inputHubNameLAD - name of the hub that receives telemetry from your configured Linux VMs. You decide this name and configure Azure accordingly.  
   Example: ```insights-telemetry-lad```

* splunkAddress               - HEC "send to" address.  
   Here's an example: ```https://YourSplunkCloudorSplunkEnterpriseAddress.cloud.splunk.com:8088/services/collector/event```
* splunkToken                 - HEC token, issued by Splunk Cloud, Splunk Enterprise UI.
* outputBinding               - "hec". There may be other output bindings in future.


## How to get the Event Hub Connection String via Azure Portal

![GetEventHubConnectionString](content/readmePic1.PNG)  
Navigate to your event hub namespace by selecting it from the list of resources in the resource group.  

### Select the security policy that your function should use

![SelectSecurityPolicy](content/readmePic2.PNG)  
The RootManageSharedAccessKey has full control over every hub in the namespace. You only need 'read' access.  

### Copy the connection string

![CopyTheConnectionString](content/readmePic3.PNG)  
Copy the connection string, paste it into the settings dialog on deployment.

## Deployment Settings form

![DeploymentSettingsForm](content/readmePic4.PNG)  
Fill in the settings values, check the box to accept T&C's, and Purchase. Here's the [page](https://azure.microsoft.com/en-us/pricing/details/functions/) describing the pricing model for Azure Functions.
