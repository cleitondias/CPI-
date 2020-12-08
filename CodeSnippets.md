Note that this Code Snippets file will be added to as more videos are added to the Getting Started with CPIS video series.

###Video 3 CPIS - Cloud Integration Add Flow Steps

```groovyscript

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
