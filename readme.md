# Slyce Web SDK V2

### Introduction

Slyce Web SDK V2 introduces new improved tools for Slyce Visual Search execution. It can be used with all modern desktop and mobile browsers. 

In order to use Slyce Web SDK V2 you’ll need *accountId*, *apiKey*, and *spaceId*. Please contact your manager to get those.

Using the SDK you could provide an image to Slyce system which would analyze it and return results constantly providing updates on process execution. Also there’s an option to enable the “UI Mode” which would create a layer over the HTML page layout showing selected image and loader. 

Slyce Web SDK V2 constantly gathers useful event analytics related to Slyce Workflows executions.

Demo page: [https://slyce-inc.github.io/sdk.web.v2.demo/demo](https://slyce-inc.github.io/sdk.web.v2.demo/demo)

### Setup

Copy/paste the following HTML code into the page you created.  
**Note:** Adding the CSS file is optional if you don't want to use the "UI Mode".

```html
<script type="text/javascript" src="https://cdn.slyce.it/websdk/v2/slyce.sdk.gz.js"></script>
<!-- Adding the CSS file is optional if you don't want to use the "UI Mode" -->
<link rel="stylesheet" href="https://cdn.slyce.it/websdk/v2/slyce.ui.css">
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
    'http://testurl.com/test.jpg', // this can be image File, image URL
    'your_workflow_id',
    {
        onTaskUpdated: function(message){}, 
        onTaskCompleted: function(results, errors){}
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
**callbacks** - an object with callback functions to execute on corresponding event { **onTaskUpdated**, **onTaskCompleted** }  

* onTaskUpdated will be fired each time the Slyce system has an update, unless the operation has been completed
* onTaskCompleted will be fired if the system finished the workflow execution or faced an error

**uiMode** - a boolean that indicates whether an overlay with uploaded image and a loading spinner should appear above all other elements during the workflow execution

**Response:**
Returns nothing.
