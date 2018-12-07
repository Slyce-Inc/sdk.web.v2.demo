# Slyce Web SDK V2

### Introduction

Slyce Web SDK V2 introduces new improved tools for Slyce Visual Search execution. It can be used with all modern desktop and mobile browsers. 

In order to use Slyce Web SDK V2 you’ll need *accountId*, *apiKey*, and *spaceId*. Please contact your manager to get those.

Using the SDK you could provide an image to Slyce system which would analyze it and return results constantly providing updates on process execution. Also there’s an option to enable the “UI Mode” which would create a layer over the HTML page layout showing selected image and loader. 

Slyce Web SDK V2 constantly gathers useful event analytics related to Slyce Workflows executions.

Demo page: [https://slyce-inc.github.io/sdk.web.v2.demo/demo](https://slyce-inc.github.io/sdk.web.v2.demo/demo)

Another demo app to play with [https://github.com/Slyce-Inc/sdk.web.v2.simple-demo-app](https://github.com/Slyce-Inc/sdk.web.v2.simple-demo-app)

### Setup

Copy/paste the following HTML code into the page you created.  
**Note:** Adding the CSS file is optional if you don't want to use the "UI Mode".

```html
<script type="text/javascript" src="https://cdn.slyce.it/websdk/v2.x/slyce.sdk.gz.js"></script>
<!-- Adding the CSS file is optional if you don't want to use the "UI Mode" -->
<link rel="stylesheet" href="https://cdn.slyce.it/websdk/v2.x/slyce.ui.css">
```

Now an SDK instance can be created.

```javascript
var sdk = new slyceSDK();
```

### Methods

##### initSlyceSpace()

```javascript
initSlyceSpace(accountId, apiKey, spaceId);
```

initSlyceSpace() sets credentials that will be used for all upcoming requests and Space operations. It returns an object with information about analytics, catalogs, lenses, mappings, workflows and endpoints related to given Space.

**Params**:  
**accountId** - Account ID provided by Slyce team **(required)**  
**apiKey** - API Key provided by Slyce team **(required)**  
**spaceId** - Space ID that is going to be used in the application **(required)**  


**Response**:  
Method returns a native Promise containing information about the operations the given Space can interact with.

```javascript
sdk.initSlyceSpace('your_account_id', 'your_api_key', 'your_space_id')
    .then(function(response){
        cnsole.log(response);
    })
    .catch(function(error){
        console.error(error);
    });
    
// initSlyceSpace() response object
// {
//    analytics: {},
//    catalogs: {},
//    lenses: {},
//    mappings: {},
//    workflows: {}
// }
```

##### executeWorkflow()

```javascript
executeWorkflow(imageData, workflowId, callbacks, uiMode)
```

executeWorkflow() opens a WebSocket connection and dispatches a message with provided image. Server emits messages with relevant data related to the Workflow process. Slyce SDK formats the message and passes it into onTaskUpdated callback function:

```javascript
sdk.executeWorkflow(
    'http://testurl.com/test.jpg', // this can be image File or image URL
    'your_workflow_id',
    {
        onTaskUpdated: function(message){}, 
        onTaskCompleted: function(results, errors){},
        afterImageProcessed: function(base64) {}
    },
    false
)

// Example of message object
{
    "jobId" : "",           
    "progress" : {} // optional        
};

// Example of results object
{
    "jobId" : "",
    "results" : [ // can be empty
        { 
            "cursor": "",
            "mappingId" : "",
            "type": "",
            "items": []
        }
    ]
};

// errors is an array of error messages
["Error 1", "Error 2"];
```

**Params:**  
**imageData** - can be an URL pointing to an image or an image File **(required)**  
**workflowId** - workflow ID to execute (supported Workflow types  are “3D Workflow” and “Universal Workflow”) **(required)**  
**callbacks** - an object with callback functions to execute on corresponding event { **onTaskUpdated**, **onTaskCompleted**, **afterImageProcessed** }  

* onTaskUpdated will be fired each time the Slyce system has an update, unless the operation has been completed
* onTaskCompleted will be fired if the system finished the workflow execution or faced an error
* afterImageProcessed use this callback if you pass File object as imageData. The SDK would process the File, fetch the base64, and rotate the image if needed. Once it's done the SDK would pass the base64 string back so you can use it in your UI

**uiMode** - a boolean that indicates whether an overlay with uploaded image and a loading spinner should appear above all other elements during the workflow execution. **Please note that you should not alter the overlay styles or behavior as the provided layout may change over time.** Implement a custom loading screen if you need an unique experience

**Response:**
Returns nothing.

##### cancelWorkflowExecution()

cancelWorkflowExecution() closes currently open WebSocket connection and cancels Workflow execution.

```javascript
sdk.cancelWorkflowExecution()
```

**Params:**
No params.

**Response:**
Returns nothing.

##### findSimilar()

findSimilar() searches for items similar to the given item in the given dataset.
In order the findSimilar() to work itemId or itemImageUrl (or both) should be provided.

```javascript
sdk.findSimilar(datasetId, itemId, itemImageUrl, settings);
```

**Params:**
**datasetId** - dataset ID against which the search chould happen **(required)**  
**itemId** - ID of the target item (note: itemId is required if itemImageUrl is not provided)
**itemImageUrl** - image URL of the target item (note: itemImageUrl is required if itemId is not provided)
**settings** - a settings object

**Response:**
Method returns a native Promise containing information about items found.

##### dispatchAnalyticsEvents()

dispatchAnalyticsEvents() invokes an XMLHttpRequest and sends all Slyce Analytics events that were queued at the moment. The SDK tracks analytics events and send them to the server each 30 seconds then the queue would be cleared and new iteration of tracking starts. You may want to use this method to send the remaining analytics events (the last iteration) before redirecting to another page or before user closes the browser tab.

```javascript
sdk.dispatchAnalyticsEvents();
```

**Params:**
No params.

**Response:**
Method returns a native Promise.
