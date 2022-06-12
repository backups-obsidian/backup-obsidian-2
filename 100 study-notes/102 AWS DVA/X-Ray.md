---
created: 2022-05-16 12:39
updated: 2022-05-19 16:53
---
---
**Links**: [[102 AWS DVA Index]]

---
## X-Ray
- Debugging monolith is easy, debugging distributed services is hard
- X-Ray helps in **visual analysis** of our application.
- **Advantages**: 
	- Troubleshooting performance (*bottlenecks*)
	- *Understand dependencies* in a microservice architecture
	- *Pinpoint* service issues
	- Review request behaviour
	- Find errors and exceptions
	- Are we meeting time SLA?
	- *Where I am throttled*?
	- Identify users that are impacted

- **Compatibility**: 
	- *Lambda*
	- Elastic Beanstalk: we use the `.ebextensions` directory to include X-Ray in BeanStalk.
	- ECS
	- ELB
	- API Gateway
	- *EC2 Instances* or any application server (even **on premise**)

- X-Ray *leverages tracing*. Tracing is an end to end way to *following a request.*
- Tracing is made of *segments and sub segments*
- *Service map is computed* from all the segments and traces 
- X-Ray is *graphical*, so even non technical people can help troubleshoot

### How to enable X-Ray
- Your code (Java, Python, Go, Node.js, .NET) **must import the AWS X-Ray SDK**
	- Very **little code modification needed**. Many SDKs only require configuration changes.
		- ![[attachments/Pasted image 20220519154314.png]]
	- The application SDK will then capture:
		- Calls to AWS services
		- HTTP / HTTPS requests
		- *Database Calls* (MySQL, PostgreSQL, DynamoDB)
		- Queue calls (SQS)
- **Install the X-Ray daemon** or **enable X-Ray AWS Integration** 
	- X-Ray daemon works as a low level UDP packet interceptor (Linux / Windows / Mac...)
	- *AWS Lambda/other AWS services already run the X-Ray daemon* for you
	- Each application must have the **lAM rights** to write data to X-Ray
- Diagram
	- ![[attachments/Pasted image 20220519152456.png]]
	- The SDK sends the trace data to a daemon, which collects segments for multiple requests and uploads them in batches.

### X-Ray troubleshooting
- If X-Ray is *not working on EC2*
	- Ensure the *EC2 IAM Role* has the proper permissions
	- Ensure the EC2 instance is *running the X-Ray Daemon*
- To enable on *AWS Lambda*:
	- Ensure it has an *lAM execution role* with proper policy (AWSX-RayWriteOnlyAccess)
	- Ensure that *X-Ray is imported in the code*
	- No need to check for daemon as it comes installed.

### Concepts
- *Segments*: each application/service will send them
- *Subsegments*: if you need *more details in your segment*
- *Trace*: *segments collected together* to form an end-to-end trace
- *Sampling*: decrease the *amount of requests sent to X-Ray*, reduce cost
- *Annotations*: *Key Value pairs* used to **index traces** and use with *filters*
- *Metadata*: *Key Value pairs*, **not indexed**, not used for searching

> [!important]- The X-Ray daemon/agent has a **config to send traces cross account**
> - Make sure the *IAM permissions* are correct the agent will assume the role
> - This allows to have a *central account for all your application tracing*
> ---
> Create an IAM role in the central account, then create IAM roles in the other accounts to assume this IAM role.