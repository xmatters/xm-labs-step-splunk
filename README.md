# xM Labs Send to Splunk Step
This step ships event data to the HTTP Event Collector in Splunk, enabling complex reporting, dashboarding and analysis of events in xMatters. 
Using the data sent from xMatters, you can build dashboards such as this one:

<kbd>
  <img src="/media/Dashboard.png" />
</kbd>

[Source](xMattersDashboard.xml)


---------

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

---------

# Files

* [xMattersDashboard.xml](xMattersDashboard.xml) - Example dashboard XML file 
* [SplunkLogo.png](SplunkLogo.png) - Logo for the Splunk step

# Splunk Setup
## Create xMatters Source Type 

The source type is a way for Splunk to categorize and format the data being ingested.  

Login to Splunk and click the Settings menu in the upper left, then head over to the Source Types. 

<kbd>
  <img src="/media/SourceType.png"  />
</kbd>

Create a new Source Type called `xmatters_events`, give it a description and hit Save.

<kbd>
  <img src="/media/SourceType1.png" width=600 />
</kbd>


## Verify HEC is Enabled 

Now, we need to set up the Collector so that Splunk can listen for new "events" via REST API. Note that Splunk considers each datum that comes in as an “event”, so the term “event” is used often in this document. It’s not my fault.  

This step may or may not be needed in a customer environment as they might have it set up already. If they do not have it activated, you will need to discuss with the customer if they are ok with it being enabled as it could be considered a low security risk. You are technically opening a hole in Splunk that could be exploited. If they are not ok with that, other ways of getting the event payload in will need to be discussed. When we get xAgent support for steps, writing to a local file ingested by Splunk might be an option.  

Click the Splunk logo again and click Data inputs.  

<kbd>
  <img src="/media/DataInputs.png" />
</kbd>

Splunk shows us all the different modes it can ingest data which are cool, but we want HTTP. We need to make sure it is enabled, so click the **HTTP Event Collector**: 

<kbd>
  <img src="/media/HECInput.png" width=600 />
</kbd>
    
And if you see an angry badge, it indicates the Collector is disabled globally. This can be cleared up by clicking the Global Settings button which conveniently shows the Global Settings dialog. Click the Enabled toggle to enable the HTTP collector and then click Save.  

<kbd>
  <img src="/media/GlobalSettings.png" width=600 />
</kbd>


## Create Collector 

Now, we need to actually add the Collector, so click the **Add new** link next to HTTP Event Collector list.  

<kbd>
  <img src="/media/HECInput.png" width=600 />
</kbd>

The wizard will walk you through the setup.

<kbd>
  <img src="/media/HECWiz1.png" width=600 />
</kbd>  

Select the `xmatters_events` source type we created before in the Input Settings dialog 

<kbd>
  <img src="/media/HECWiz2.png" width=600 />
</kbd>

Review and Submit. A token value will be displayed. Copy this value with care, it is a password.

<kbd>
  <img src="/media/HECWiz4.png" width=600 />
</kbd>

## Create Dashboard (optional)

This step is for creating the default dashboard that can serve as an example on how to query for the Splunk data. 
Navigate over to the Search & Reporting App in Splunk. Then find the Dashboards button and click Create New Dashboard

<kbd>
  <img src="/media/NewDashboard.png" width=600 />
</kbd>


Give it a good name and a description. Seriously, all your friends will thank you. Especially if you share the dashboard with them. 


<kbd>
  <img src="/media/NewDashboard1.png" width=500 />
</kbd>


There is a helpful wizard for adding panels and such, but I took care of that for you. So just click the Source button, remove all the text there and paste in the [xMattersDashboard.xml](xMattersDashboard.xml) file. 

<kbd>
  <img src="/media/DashboardSource.png" width=600 />
</kbd>

Clicking the Save button will execute the searches and display the results which will all be empty for now because we haven't added any data.  



# Flow Designer Steps

## Import Steps
The steps are packaged into the [SplunkHECSteps.zip](SplunkHECSteps.zip) workflow file making for easy importing. 

1. Login to xMatters and navigate to the Workflow tab. 
2. Click the Import button and import the [SplunkHECSteps.zip](SplunkHECSteps.zip) file. 

The workflow contains two steps:
* `xM - Get event details` which is used to retrieve details of the event
* `Splunk HEC` which is used to send data to Splunk

Both steps will be needed. 

## Constants
Adding a constant to hold the token value means you don't have to type it in every time you want to use it. This way, you just drag the constant in as an input to the Splunk step. 
Exit the flow designer and head over to the integration builder and click the **Edit Constants** button and paste in the value from above and hit **Save Changes**. 

<kbd>
  <img src="/media/Constant.png" width=500 />
</kbd>

# Usage
The design of these steps is such that using the event status trigger is best. This will ensure you have the initial event data as soon as it happens, and then **all** of the response and notification data. Using other triggers may be possible depending on what information you need to get and when. 

The steps have been imported with the workflow, but as is they don't do anything interesting. They need to be added to a canvas to ship data from that canvas to Splunk. So open up your favorite canvas and drag the steps

Add these to the event status trigger in this order:

<kbd>
  <img src="/media/Flow1.png" width=400 />
</kbd>

Then double click on the get events step and drag in the inputs like so:

<kbd>
  <img src="/media/Flow-setup1.png" width=600 />
</kbd>

On the endpoint tab, select the xMatters endpoint

<kbd>
  <img src="/media/Flow-setup2.png" width=600 />
</kbd>


Then, double click on the Splunk HEC step and map the inputs, pulling the token from the Constants section and the eventJSON from the Get Event Payload step like so. 

<kbd>
  <img src="/media/Flow-setup3.png" width=600 />
</kbd>

Make sure to drag all of the eventJSON properties to the corresponding input. (Each property in the stream can handle a max of 20,000 characters, but an event can be more than 20,000 so we break it up into chunks of 20,000. If an event is more than 80,000 characters an error will be thrown and you should add more inputs as appropriate.)

<kbd>
  <img src="/media/Flow-setup3.2.png" width=600 />
</kbd>


On the Run Location tab, make the appropriate selection depending on how xMatters will access your Splunk environment. If you are running Splunk in the cloud or have an open firewall connection, then select Cloud. Otherwise, select an xMatters Agent that will have access to Splunk. 

<kbd>
  <img src="/media/Flow-setup3.5.png" width=600 />
</kbd>


On the endpoint tab, create a new endpoint called Splunk HEC and the Base URL will be the url of your Splunk hostname prefixed with input- and suffixed by :8088. 
For example, if your Splunk url is 

`https://prd-p-cqzf26jjxqbp.cloud.splunk.com`

Then, your Base URL is:

`https://input-prd-p-cqzf26jjxqbp.cloud.splunk.com:8088`

One last thing, it seems that Splunk cloud instances will need to check the Trust Self Signed Certificates in the endpoint. 

<kbd>
  <img src="/media/Flow-setup4.png" width=500 />
</kbd>

