---
created: 2022-05-16 12:39
updated: 2023-02-16 08:48
---
---
**Links**: [[102 AWS DVA Index]]

---
## Synchronous Invocation
- We **wait for the results of the invocation**.
- *Results are returned right away*
- **Error handling must happen client side** (retries, exponential backoff, etc...)
	- ![[attachments/Pasted image 20220523152943.png]]
- Services which can do synchronous invocation
	- User Invoked:
		- *Elastic Load Balancing* (Application Load Balancer)
		- Amazon *API Gateway*
		- Amazon CloudFront (*Lambda@Edge*)
		- Amazon S3 Batch
	- Service Invoked:
		- *Cognito*
		- AWS *Step Functions*
		- *Kinesis Data Firehose*
	- CLI/SDK can be used for both synchronous and asynchronous invocations
		- ![[attachments/Pasted image 20230216084734.png]]



### Lambda with ALB
- To expose a **Lambda function as an HTTPS endpoint** we can use the *Application Load Balancer* or an *API Gateway*.
- The Lambda function must be *registered in a target group*
	- ![[attachments/Pasted image 20220523154329.png]]
- ALB can invoke Lambda with the help of the **resource based policy** of Lambda.

#### HTTP to JSON and JSON to HTTP
> [!question]- How does ALB convert an HTTP request to a lambda invocation?
> - From ALB to Lambda the HTTP request gets transformed to a JSON document.
> - We get all the information present in the HTTP request in the **event** parameter of the function handler. 
> - Like *Query String Parameters as Key/Value pairs*, *Headers as Key/Value pairs*, *Body* (for POST, PUT...) is *Base64Encoded*.
> ---
> ![[attachments/Pasted image 20220523154559.png]]

- The JSON document returned by lambda is converted to an HTTP request by the ALB. 
	- In lambdas response we get *status code* & description, *headers as key value pairs* and *body* which is base64 encoded.
	- The lambda must return these fields if we want the response to be displayed in the browser. Like if content-type header is not present then our browser won't know how to display the content and will download the response instead.
	- ![[attachments/Pasted image 20220523154838.png]]

#### ALB Multi Header Values
- ALB can support multi header values (**ALB setting**)
- When you enable multi-value headers, HTTP headers and query string parameters that are sent with multiple values are **shown as arrays** within the AWS Lambda event and response objects.
	- ![[attachments/Pasted image 20220523155036.png]]
