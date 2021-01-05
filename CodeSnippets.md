Note that this Code Snippets file will be added to as more videos are added to the Getting Started with CPIS video series.

### Video 2 CPIS - Onboarding, Suite Activation

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
	}
	 
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

### Video 13 CPIS - XSUAA Policy (API Management)

API Policy: SAP Cloud Foundry XSUAA JWTToken > Scripts > config.js

```javascript

context.setVariable("sapapim.clientId",context.proxyRequest.headers.clientId);
context.setVariable("sapapim.secret",context.proxyRequest.headers.secret);

```

### Video 20 CPIS - Adding a Filter to the Application

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

Code for New Sales By Country Resource, API Designer

* Go to https://swagger.io/docs/specification/basic-structure/ for more information on the OpenAPI Specification.

```yaml

  '/srv/salesbycountry/{id}':
    get:
      tags:
        - SalesByCountry
      description: Filter Items by Country ID
      parameters:
        - in: path
          name: id
          required: true
          schema:
            type: integer
            minimum: 1
            maximum: 99
          description: Country ID
      responses:
        default:
          description: error payload
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

```


### Video 9 CPIS - Adding Managed CFApp API to an Integration Flow

[Value Mapping File](https://github.com/saphanaacademy/cpis/blob/main/resources/valuemappingCountryCodeToId.csv)
