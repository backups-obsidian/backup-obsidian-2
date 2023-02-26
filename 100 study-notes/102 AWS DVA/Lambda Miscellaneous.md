---
created: 2023-02-25 14:17
updated: 2023-02-25 14:17
---
---
**Links**: [[102 AWS DVA Index]]

---
## Miscellaneous
- We use *DLQs/Destinations* with Lambda in **asynchronous** and **event source mapping**.
- For *logging*:
	- For *each lambda a log group* is created. 
	- The logs are then grouped into streams. We can find the logs if we go inside a particular stream.
- **Best Practices**:
	- *Perform heavy-duty work outside of your function handler*
		- Connect to databases outside of your function handler
		- Initialise the AWS SDK outside of your function handler
		- Pull in dependencies or datasets outside of your function handler
	- Use *environment variables* for:
		- Database Connection Strings, S3 bucket, etc... don't put these values in your code
		- Passwords, sensitive values, etc can be encrypted using KMS
	- *Minimise your deployment package size to its runtime necessities*
		- Break down the function if need be
		- Remember the AWS Lambda limits
		- *Use Layers* where necessary
- **Avoid using recursive code, never have a Lambda function call itself**. This can become very expensive.