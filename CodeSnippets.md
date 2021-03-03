Note that this Code Snippets file will be added to as more videos are added to the Getting Started with CPIS video series.

### Video 2 CPIS - Onboarding, Suite Activation

The following Role Collections are required to follow along with this video series. Please note that this list is subject to change.

- APIManagement.SelfService.Administrator
- APIPortal.Administrator	
- APIPortal.Service.CatalogIntegration
- Cloud Connector Administrator
- OpenConnectors_User
- PI_Administrator
- PI_Business_Expert	
- PI_Integration_Developer	
- trial-content-developer

JSON for Process Integration Runtime

```JSON
{
 "roles":[
   "ESBMessaging.send"
 ]
}
```

### Video 5 CPIS - Running Flows (Cloud Integration)

GroovyScript for Logging

```javascript

import com.sap.gateway.ip.core.customdev.util.Message
import java.util.HashMap

def Message processData(Message message) {
	
	def body = message.getBody(java.lang.String) as String
	def messageLog = messageLogFactory.getMessageLog(message)
	if(messageLog != null){
		messageLog.addAttachmentAsString("Incoming Message", body, "text/xml")
	}
	 
	return message
}

```

### Video 9 CPIS - Policies (API Management)

API Policy: Proxy Endpoint > Preflow > Mediation Policies > Assign Message (Incoming Request) > AddHeaderAPIKey

```xml

<AssignMessage async="false" continueOnError="false" enabled="true" xmlns='http://www.sap.com/apimgmt'>
    <Add>
        <Headers>
            <Header name="apikey">YourAPIKeyGoesHere</Header>
        </Headers>
    </Add>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <AssignTo createNew="false" type="request"></AssignTo>
</AssignMessage>

```

### Video 14 CPIS - XSUAA Policy (API Management)

API Policy: SAP Cloud Foundry XSUAA JWTToken > Scripts > config.js

```java

context.setVariable("sapapim.clientId",context.proxyRequest.headers.clientId);
context.setVariable("sapapim.secret",context.proxyRequest.headers.secret);

```

### Video 15 CPIS - Adding a Filter to the Application

Code Block to Insert Below SRV Sales

```javascript

app.get('/srv/salesbycountry/:id', function (req, res) {
    var p = parseInt(req.params.id);
    req.db.exec('SELECT * FROM "myapphana.db::sales" WHERE "id" = ?', [p], function (err, results) {   
        if (err) {               
            res.type('text/plain').status(500).send('ERROR: ' + err.toString());
            return;
        }
        res.status(200).json(results);
    });
});

```

### Video 16 CPIS - Adding a Filtered Resource to the Managed API

Code for New Sales By Country Resource, API Designer

* Go to https://swagger.io/docs/specification/basic-structure/ for more information on the OpenAPI Specification.

```yaml

      parameters:
        - in: path
          name: id
          required: true
          example: 1
          schema:
            type: integer
            minimum: 1
            maximum: 99
          description: Country ID
        - in: header
          name: clientId
          required: true
          schema:
            type: string
          description: Client ID

```

### Video 19 CPIS - Value Mapping

[Value Mapping File](https://github.com/saphanaacademy/cpis/blob/main/resources/valuemappingCountryCodeToId.csv)


### Video 20 CPIS - Adding Value Mapping to an Integration Flow

Groovy Script for Value Mapping

```javascript

import com.sap.gateway.ip.core.customdev.util.Message
import java.util.HashMap
import com.sap.it.api.ITApiFactory
import com.sap.it.api.mapping.ValueMappingApi
// mapping.ValueMappingApi will be used to execute value mapping with the given parameters

def Message processData(Message message) {
   def map = message.getProperties()
   def valueMapApi = ITApiFactory.getApi(ValueMappingApi.class, null)
   
   // Step 1: assign message property vars
   def inputProperty = "yourInputProperty"
   def outputProperty = "yourOutputProperty"
   
   // the Value Mapping functionality defined below
   // ValueMap (Source agency, Source identifier, Source value, Target agency, Target identifier) = Target value
   
   // Step 2: assign getMappedValue vars
   def sourceAgency = "yourSourceAgency"
   def sourceIdentifier = "yourSourceIdentifier"
   def targetAgency = "yourTargetAgency"
   def targetIdentifier = "yourTargetIdentifier" 

   def targetValue = valueMapApi.getMappedValue(sourceAgency, sourceIdentifier, map.get(inputProperty), targetAgency, targetIdentifier)
   message.setProperty(outputProperty, targetValue)
   
   return message
}


```
	
### Video 24 CPIS - Content Enricher: Combine Message Data	

Content Modifier Expression, Modify JSON

```java

{
  "objects": ${body}
}

```

Groovy Script, Modify JSON (Groovy Script Step is an option to using the Content Modifier Expression above)

```java

import com.sap.gateway.ip.core.customdev.util.Message
import java.util.HashMap
import groovy.json.*

def Message processData(Message message) {
    def jsonIn = message.getBody(String.class)
    jsonIn = jsonIn.toString()
    jsonIn = "{\"objects\":" + jsonIn + "}"
    message.setBody(jsonIn)
    return message
}

```


### Video 25 CPIS - Content Enricher: Enrich Message Data

Groovy Script, Modify JSON

```java

import com.sap.gateway.ip.core.customdev.util.Message
import java.util.HashMap
import groovy.json.*

def Message processData(Message message) {
    // message body & properties
    def jsonOrig = message.getBody(String.class)
    def jsonMod = jsonOrig.toString()
    jsonMod = jsonMod.substring(1,jsonMod.length()-1)
    def map = message.getProperties()
    
    // see the returned message structure further below for more info
    
    // Step 1: assign message property to be added as a key object
    def objKeyValue = "yourMessageProperty"
    
    // Step 2: assign object names of your choice
    def objParentName = "yourParentName"
    def objKeyName = "yourKeyName"
    def objDataArrayName = "yourDataArrayName"

    jsonMod = "{\"" + objParentName + "\": [{\"" + objKeyName + "\": \"" + map.get(objKeyValue) + "\",\"" + objDataArrayName  + "\":[" + jsonMod + "]}]}"
    message.setBody(jsonMod)
    return message
}

/*  returned JSON message structure
{
    "objParentName": [
        {
            "objKeyName": "null",
            "objDataArrayName": [
                {
                    jsonIn
                }
            ]
        }
    ]
}
*/

```
