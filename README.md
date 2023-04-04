# Dataminr Pulse Data Connector
* [Introduction](#Introduction)
* [Prerequisites](#Prerequisites)
* [Folders](#Folders)
* [Configuration](#Configuration)
* [Installing for the users](#Installing-for-the-users)
* [Installing for testing](#Installing-for-testing)

## Introduction<a name="Introduction"></a>

This folder contains the Azure function Http Trigger code for DataminrPulseAlerts Data Connector. The connector will run automatically when Alerts data will be pushed via RTAP and will ingest the data into Microsoft Sentinel logs custom table `DataminrPulse_Alerts_CL`. To receive data from Dataminr RTAP, user must need to run this Data Connector manually once to add integration settings for deployed function app in Dataminr.


## Folders<a name="Folders"></a>

1. `DataminrPulseAlerts/` - This contains the package, requirements, ARM JSON file, connector page template JSON, and other dependencies.
2. `DataminrPulseAlertsSentinelConnector/` - This contains the Azure function source code to receive ALerts data via Dataminr RTAP and post into Microsoft Sentinel along with sample data.

## Prerequisites<a name="Prerequisites"></a>
1. **Dataminr Client ID** and **Dataminr Client Secret** is required. 
2. At least one **watchlist** must be configured in Dataminr Portal to receive alerts data.

## Configuration<a name="Configuration"></a>

### STEP 1 - Create/Get Client credentials of Dataminr Pulse account.

### STEP 2 - Configure Watchlists in Dataminr Pulse portal.
1. Login to the Dataminr PulseAlerts portal.
2. Go to the Lists tab and select the type of watchlist you want to create.
3. Click on the New list.
4. Provide name of list.
5. Select the categories of alerts you wish to receive.
6. Choose the level in priority that describes your preferred alert coverage.
7. Save the newly configured list.


## Installing for the users<a name="Installing-for-the-users"></a>

After the solution is published, we can find the connector in the connector gallery of Microsoft Sentinel among other connectors in Data connectors section of Microsoft Sentinel.

i. Go to Microsoft Sentinel -> Data Connectors

ii. Click on the DataminrPulseAlerts, connector page will open.

iii. Click on Deploy to Azure 
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/sentinel-DataminrPulseAlerts-azuredeploy)


It will lead to a custom deployment page where user need to select **Subscription**, **Resource Group** and **Location**.
And need to enter below information to configure DataminrPulse data connector for Microsoft Sentinel.
```Function Name
   BaseURL
   ClientId
   ClientSecret
   AlertsTableName
   LogLevel
   Workspace ID
   Workspace Key
```
### **Post Deployment Steps**<a name="Post Deployment Steps"></a>

**Get the Function app endpoint**
    1. Follow the below steps to get the endpoint.
        - Go to Function apps → <your_function_app> → Functions → DataminrPulseAlertsSentinelConnector → Overview
        - Go to "GetFunctionUrl" and copy the function url. 

**Steps to add integration settings in Dataminr by running function app manually.**

    1. Go to Function apps → <your_function_app> → Functions → DataminrPulseAlertsSentinelConnector → Code+Test
    2. Click on Test+Run.
    3. Provide the necessary details as mentioned below:
        - HTTP Method : POST
        - Key : default(Function Key)
        - Reuqest Body (case-sensitive): 
            {
                "integration-settings": "ADD",
                "url": "<url part from copied Function-url>",
                "token": "<value of code parameter from copied Function-url>"
            }
    4. After providing all required details, click on Run and you will receive integrationSetting ID on success of adding integration settings for this integration.

## Installing for testing<a name="Installing-for-testing"></a>


i. Log in to Azure portal using the URL - [https://portal.azure.com/?feature.BringYourOwnConnector=true&feature.UseKoBladeForE2E=true#home](https://portal.azure.com/?feature.BringYourOwnConnector=true&feature.UseKoBladeForE2E=true#home).

ii. Go to Microsoft Sentinel -> Data Connectors

iii. Click the “import” button at the top and select the json file `DataminrPulseAlerts_FunctionApp.json` downloaded on your local machine from Github.

iv. This will load the connector page and rest of the process will be same as the Installing for users guideline above.


The connector should ingest the data into the logs when it receives data from Dataminr RTAP via Http request.

Each invocation and its logs of the function can be seen in Function App service of Azure, available in the Azure Portal outside of Microsoft Sentinel.

i. Go to Function App and click on the function which you have deployed, identified with the given name at the deployment stage.

ii. Go to Functions -> DataminrPulseSentinelConnector -> Monitor

iii. By clicking on invocation time, you can see all the logs for that run.

**Note: Furthermore we can check logs in Application Insights of the given function in detail if needed. We can search the logs by operation ID in Transaction search section.**
