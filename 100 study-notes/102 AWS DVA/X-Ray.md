---
created: 2022-05-16 12:39
updated: 2023-02-20 08:58
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
- We CANNOT use CloudTrail to debug and trace **data** across accounts. X-Ray is suitable for this purpose.

> [!note]- We can use X-Ray to **collect data across AWS Accounts**.

> [!important]- The AWS X-Ray SDK *DOES NOT send trace data directly to AWS X-Ray*. 
> To avoid calling the service every time your application serves a request, *the SDK sends the trace data to a daemon*, which *collects segments* for multiple requests, *buffers them* and uploads them in **batches**.

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
	- For *services that don't send their own segments like Amazon DynamoDB*, X-Ray uses subsegments to generate *inferred segments*
- **Subsegments**: if you need **more details in your segment**
	- Subsegments represent your application's view of a **downstream call as a client**.
	- *We can define arbitrary subsegments to instrument specific functions or lines of code in your application*
		- ![[attachments/Pasted image 20230220084457.png]] 
- *Trace*: *segments collected together* to form an end-to-end trace
- *Sampling*: decrease the *amount of requests sent to X-Ray*, reduce cost
- **Annotations**: *Key Value pairs* used to **index traces** and use with *filters*
	- Upto *50 annotations* per trace.
	- Use annotations to *record data* that you want to use to **group traces**. 
- *Metadata*: *Key Value pairs*, **not indexed**, not used for searching
	- *User defined data* not used for indexing.

> [!note]- Trace > Segments > Sub segments

> [!important]- The X-Ray daemon/agent has a **config to send traces cross account**
> - Make sure the *IAM permissions* are correct, the agent will assume the role
> - This allows to have a *central account for all your application tracing*
> ---
> Create an IAM role in the central account, then create IAM roles in the other accounts to assume this IAM role.

> [!question]- In order to quickly troubleshoot their systems, your manager instructed you to *record the calls that your application makes to all AWS services and resources*. You developed a custom code that will send the segment documents directly to X-Ray by using the `PutTraceSegments` API. What should you include in your segment document to meet the above requirement?
> **Subsegments**
> ---
> *Subsegments provide more granular timing information* and details about downstream calls that your application made to fulfill the original request. A subsegment can **contain additional details about a call to an AWS service**, an **external HTTP API**, or an **SQL database**.