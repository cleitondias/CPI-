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

### Video 3 CPIS - Cloud Integration Add Flow Steps

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

### Video 5 CPIS - Using API Management

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
