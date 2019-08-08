# xM Labs Send to Splunk Step
This step ships event data to the HTTP Event Collector in Splunk, enabling complex reporting, dashboarding and analysis of events in xMatters. 

---------

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

---------

# Files

* [logo.png](/media/hat.png) - Logo for the Hat Factory
* [otherfile.file](/otherfile.file) - Some other file that does something useful.

# Splunk Setup
## Create xMatters Source Type 

The source type is a way for Splunk to categorize and format the data being ingested. Customers likely won’t have a source type for xMatters data, so this will need to be done every time. Don’t worry, it isn’t complicated nor risky.  

Login to Splunk and click the Settings menu in the upper left, then head over to the Source Types. 

<kbd>
  <img src="/media/SourceType.png" />
</kbd>

Create a new Source Type called `xmatters_events`, give it a description and hit Save.

<kbd>
  <img src="/media/SourceType1.png" />
</kbd>


## Verify HEC is Enabled 

Now, we need to set up the Collector so that Splunk can listen for new "events" via REST API. Note that Splunk considers each datum that comes in as an “event”, so the term “event” is used often in this document. It’s not my fault.  

This step may or may not be needed in a customer environment as they might have it set up already. If they do not have it activated, you will need to discuss with the customer if they are ok with it being enabled as it could be considered a low security risk. You are technically opening a hole in Splunk that could be exploited. If they are not ok with that, other ways of getting the event payload in will need to be discussed. When we get xAgent support for steps, writing to a local file ingested by Splunk might be an option.  

Click the Splunk logo again and click Data inputs.  

<kbd>
  <img src="/media/DataInputs.png">
</kbd>

Splunk shows us all the different modes it can ingest data which are cool, but we want HTTP. We need to make sure it is enabled, so click the **HTTP Event Collector**: 

<kbd>
  <img src="/media/HECInput.png" />
</kbd>
    
And if you see an angry badge, it indicates the Collector is disabled globally. This can be cleared up by clicking the Global Settings button which conveniently shows the Global Settings dialog. Click the Enabled toggle to enable the HTTP collector and then click Save.  

<kbd>
  <img src="/media/GlobalSettings.png" />
</kbd>


## Create Collector 

Now, we need to actually add the Collector, so click the **Add new** link next to HTTP Event Collector list.  

<kbd>
  <img src="/media/HECInput.png" />
</kbd>

The wizard will walk you through the setup.

<kbd>
  <img src="/media/HECWiz1.png" />
</kbd>  

Select the `xmatters_events` source type we created before in the Input Settings dialog 

<kbd>
  <img src="/media/HECWiz2.png" />
</kbd>

Review and Submit. A token value will be displayed. Copy this value with care, it is a password.

<kbd>
  <img src="/media/HECWiz4.png" />
</kbd>



# Flow Designer Steps

## Action Name 1
The "Create App Record" step creates a record in the `APPLICATION_NAME` and such and such. 

### Settings
Values for the settings tab. A screen shot is also acceptable. If you have a custom icon, please upload it into the `/media` folder and reference with `<kbd> <img src="/media/hat.png"></kbd>`. See below for the format of a table in markdown. 

| Field | Value |
| ----- | ----- |
| Name | Create a Hat |
| Description | Sends a create a hat request to the hat factory.  |
| Icon | <kbd> <img src="/media/hat.png"></kbd> |
| Include Endpoint | Yes |
| Endpoint Type | Basic Auth |
| Endpoint Label | Hat Factory |



### Inputs
Inputs should be in the form of tables. The syntax looks like this in the markdown. The "content cells" do not have to line up, just make sure you have the right number of columns in each row. Required inputs must be at the top and please for the love of Pete, add some helpful text as to what the input is for or what it does. If the step only allows certain values, make sure to list them!

```
| Name  | Required? | Min | Max | Help Text | Default Value | Multiline |
| ----- | ----------| --- | --- | --------- | ------------- | --------- |
| Name  | Yes | 0 | 2000 | This is the color of the hat to create | Blue | No |
| Color | No | 0 | 2000 | The major color of the hat. Possible values are Red, White, Blue | Blue | No |
```

| Name  | Required? | Min | Max | Help Text | Default Value | Multiline |
| ----- | ----------| --- | --- | --------- | ------------- | --------- |
| Name  | Yes | 0 | 2000 | This is the color of the hat to create | Blue | No |
| Color | No | 0 | 2000 | The major color of the hat. Possible values are Red, White, Blue | Blue | No |


### Outputs

| Name |
| ---- |
| Hat |
| Link |

### Script

```javascript
// Retrieve the name and color values
var name = input['Name'];
var color = input['Color'];

// Make the request
var req = http.request({
   "endpoint": "Hat Factory",
   "path": "/hat",
   "method": "POST",
   "headers": {
      "Content-Type": "application/json"
   }
});

var hatPayload = {
   "name": name,
   "color": color
};

var resp = req.write( hatPayload );

```


## Action Name 2
The "Update App Record" step creates a record in the `APPLICATION_NAME` and such and such. 

### Settings

| Field | Value |
| ----- | ----- |
| Name | Update a Hat |
| Description | Sends an update a hat request to the hat factory.  |
| Icon | <kbd> <img src="/media/hat.png"></kbd> |
| Include Endpoint | Yes |
| Endpoint Type | Basic Auth |
| Endpoint Label | Hat Factory |

### Inputs

| Name  | Required? | Min | Max | Help Text | Default Value | Multiline |
| ----- | ----------| --- | --- | --------- | ------------- | --------- |
| Name  | Yes | 0 | 2000 | This is the color of the hat to create | Blue | No |
| Color | No | 0 | 2000 | The major color of the hat. Possible values are Red, White, Blue | Blue | No |


### Outputs

| Name |
| ---- |
| Hat |
| Link |

### Script

```javascript
// Retrieve the name and color values
var name = input['Name'];
var color = input['Color'];

// Make the request
var req = http.request({
   "endpoint": "Hat Factory",
   "path": "/hat",
   "method": "POST",
   "headers": {
      "Content-Type": "application/json"
   }
});

var hatPayload = {
   "name": name,
   "color": color
};

var resp = req.write( hatPayload );

```
