---
created: 2023-03-01 10:50
updated: 2023-03-01 18:04
---
---
**Links**: [[102 AWS DVA Index]]

---
## Event and Context Objects
- **Event object** contains *details about the event*.
	- Any type of *input arguments*, invoking service arguments will be present in the event object.
- **Context object** contains information about **metadata of the function** like the request id, log group name, memory limit, etc.
	- Provides methods and properties that provide information about the invocation, function, and runtime environment.
- Example:
	- ![[attachments/Pasted image 20230301105407.png]]

## File system mounting
- Lambda functions can access EFS file systems *if they are running in a VPC*.
- Configure Lambda to mount EFS file systems to local directory during initialisation.
- Must *leverage EFS Access Points*.
	- ![[attachments/Pasted image 20230301175322.png]]

## Function URL
- Dedicated *HTTPs endpoint* for your Lambda function
	- A unique URL endpoint is generated for you (never changes) 
	- `https://<url-id>.lambda-url.<region>.on.aws`
- Supports *Resource-based Policies* & *CORS configurations*.
- There are 2 different types of resource based policies
	- `NONE`: allow public and unauthenticated access. The resource based policy must grant public access.
		- ![[attachments/Pasted image 20230301180324.png]]
	- `AWS_IAM`: IAM is used to authenticate and authorise requests.
		- For the *same account* either Identity based policy *OR* resource based policy is required. 
		- For *cross account* access both identity based policy *AND* resource based policy is required.
		- ![[attachments/Pasted image 20230301180406.png]]

