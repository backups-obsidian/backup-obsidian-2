---
created: 2022-05-26 16:04
updated: 2023-02-10 09:41
---
---
**Links**: [[102 AWS DVA Index]]

---
## Integration Types
### MOCK
- API Gateway *returns a response without sending the request to the backend*
- Just for *development and testing*

### HTTP/AWS (Lambda & AWS Services)
- We must configure both the *integration request* and *integration response*
- We can setup data mapping using **mapping templates** to modify the request & response.
	- This means we will be able to change the request made to the backed and change the response received from the backend before sending it to the client.
	- Example: mapping the REST API to an API call on SQS Queue *so that SQS queue can understand the API call made by the API Gateway*.
	- ![[attachments/Pasted image 20220526155102.png]]

### AWS_PROXY (Lambda Proxy)
- Incoming request from the client is the input to Lambda
- The **function is responsible for the logic of request/response**. 
- *All the work is done on the backend* and API Gateway is there just to proxy the request.
- *No mapping template*, headers, query string parameters, etc are *passed as arguments*
	- ![[attachments/Pasted image 20220526155343.png]]
	- ![[attachments/Pasted image 20220526155113.png]]

### HTTP_PROXY
- *No mapping template*
- The HTTP request is passed to the backend
- The **HTTP response from the backend is forwarded by API Gateway**
- API Gateway is used just to proxy the request
	- ![[attachments/Pasted image 20220526155619.png]]

## Mapping Templates
- So *mapping templates* can only be used if we use HTTP/AWS services **without proxy**.
- Mapping templates can be used to **modify request/responses**
- Type of modification that can be done:
	- *Rename/Modify query string parameters*
	- Modify body content
	- *Add headers*
- Uses **Velocity Template Language (VTL)**: it a scripting language for writing these templates.
- *Filter output results* (remove unnecessary data)

> [!tip]- In general it is *better to use proxy integration types* (either lambda or HTTP)
> Otherwise you have to learn another scripting language just to modify the request and responses.

### Use cases: JSON to XML with SOAP
- SOAP API are XML based, whereas REST API are JSON based
	- ![[attachments/Pasted image 20220526160129.png]]
- In this case, API Gateway should:
	- Extract data from the request: either path, payload or header
	- *Build SOAP message based on request data* (**mapping template**)
	- Call SOAP service and receive XML response
	- *Transform XML response to desired format* (like JSON), and respond to the user

### Use cases: Renaming Variables
![[attachments/Pasted image 20220526160341.png]]