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

import com.sap.gateway.ip.core.customdev.util.Message;
import java.util.HashMap;

def Message processData(Message message) {
	
	def body = message.getBody(java.lang.String) as String;
	def messageLog = messageLogFactory.getMessageLog(message);
	if(messageLog != null){
		messageLog.addAttachmentAsString("Incoming Message", body, "text/xml");
	};
	 
	return message;
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

```javascript

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

<BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR><BR>                 
### NOTE: The Code Below is for Upcoming Videos


### Video 18 CPIS - Value Mapping

[Value Mapping File](https://github.com/saphanaacademy/cpis/blob/main/resources/valuemappingCountryCodeToId.csv)


### Video 19 CPIS - Adding Value Mapping to an Integration Flow

Groovy Script for Value Mapping

```javascript

import com.sap.gateway.ip.core.customdev.util.Message;
import java.util.HashMap;
import com.sap.it.api.ITApiFactory;
import com.sap.it.api.mapping.ValueMappingApi;
// mapping.ValueMappingApi will be used to execute value mapping with the given parameters

def Message processData(Message message) {
   def map = message.getProperties();
   def valueMapApi = ITApiFactory.getApi(ValueMappingApi.class, null);
   
   // Step 1: assign message property vars
   def inputProperty = "yourInputProperty";
   def outputProperty = "yourOutputProperty";
   
   // the Value Mapping functionality defined below
   // ValueMap (Source agency, Source identifier, Source value, Target agency, Target identifier) = Target value;
   
   // Step 2: assign getMappedValue vars
   def sourceAgency = "yourSourceAgency";
   def sourceIdentifier = "yourSourceIdentifier";
   def targetAgency = "yourTargetAgency";
   def targetIdentifier = "yourTargetIdentifier";   

   def targetValue = valueMapApi.getMappedValue(sourceAgency, sourceIdentifier, map.get(inputProperty), targetAgency, targetIdentifier);
   message.setProperty(outputProperty, targetValue);  
   
   return message;
}


```


### Video 24 CPIS - Combining Message Data

Groovy Script for JSON to XML

```javascript

import com.sap.gateway.ip.core.customdev.util.Message;
import java.util.HashMap;
import groovy.json.*;

def Message processData(Message message) {
    // message body 
    def jsonIn = message.getBody(String.class);
    jsonIn = jsonIn.toString();
    jsonIn = jsonIn.substring(1,jsonIn.length()-1);
    // message properties 
    def map = message.getProperties();
    
    // see the guide below for more information on assigning vars
    
    // Step 1: assign message property to elemKey
    def elemKey = "youKeyElement";
    
    // Step 2: assign element names
    def elemParentName = "yourParentElement";
    def elemKeyName = "yourKeyElementName";
    def elemDataName = "yourDataElementName";

    def xmlOut = "{\"" + elemParentName + "\": [{\"" + elemKeyName + "\": \"" + map.get(elemKey) + "\",\"" + elemDataName  + "\":[" + jsonIn + "]}]}";
    message.setBody(xmlOut);
    
    return message;
}

/*

---------------------------
Example for Var Assignments
---------------------------

    // message property for elemKey
    def elemKey = "inCountryCode";
    
    // element names
    def elemParentName = "cfApp";
    def elemKeyName = "countryCode";
    def elemDataName = "countryInfo";
    
-------------------------
Example for Script Inputs
-------------------------

    1) Input Message Body (JSON from Cloud Foundry Application)
    
        [
            {
                "id": 1,
                "region": "Europe",
                "country": "France",
                "amount": 123
            }
        ]
    
    2) Input Message Property Value (Assigned from Sender Message Body)
    
        "FRA"
        
-------------------------
Example for Script Output
-------------------------        

    3) Output from Grooy Script
    
        <?xml version='1.0' encoding='UTF-8'?>
        <root>
            <cfApp>
                <countryCode>FRA</countryCode>
                <countryInfo>
                    <id>1</id>
                    <region>Europe</region>
                    <country>France</country>
                    <amount>123</amount>
                </countryInfo>
            </CFApp>
        </root>
        
-------------------------
Schema for Message Output
-------------------------

    <root>
        <elemParent>
            <elemKey>key</elemKey>
            <elemData>
                <></>
            </elemData>
        </elemParent>
    </root>           

*/

```
