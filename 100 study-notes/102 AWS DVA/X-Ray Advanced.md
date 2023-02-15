---
created: 2022-05-19 15:50
updated: 2023-02-14 19:52
---
---
**Links**: [[102 AWS DVA Index]]

---
## Sampling
- With sampling rules, you **control the amount of data that you record**
- You can *modify sampling rules without changing your code*
- By *default*, the X-Ray SDK records the **first request each second**, and **five percent of any additional requests**.
	- *One request per second* is the **reservoir**, which ensures that *at least one trace is recorded* each second as long the service is serving requests.
	- *Five percent* is the **rate** at which *additional requests beyond the reservoir size are sampled*.
	-  For example, if you set the *reservoir size to 50* and *fixed rate to 10%*, then if *100 requests per second* match the rule, the *total sampled is 55 requests per second*.
	- ![[attachments/Pasted image 20220519155053.png]]
	- *Rate of 1 means all requests* should be sent to X-Ray. This is only good for debugging and will be very expensive in production.

- You can *create your own rules* with the reservoir and rate
- We can define many sampling rules with *different priority*. **Lower priority** takes *precedence*.
- We can also specify the **matching criteria** which determines *what requests to match*.

> [!note] You can very quickly change the sampling rules from the X-Ray console and there is *no need of restarting the application*. Daemon knows how to fetch the new sampling rules.

> [!tip]- If you are *getting a huge AWS bill* for AWS X-Ray then try *enabling sampling*.

## X-Ray APIs
### X-Ray Write APIs 
- Used by the **X-Ray daemon**
	- ![[attachments/Pasted image 20220519155927.png]]
- **PutTraceSegments**: *Uploads segment* documents to AWS X-Ray
- **PutTelemetryRecords**: Used by the AWS X-Ray daemon to *upload telemetry*.
	- SegmentsReceivedCount, SegmentsRejectedCounts, *BackendConnectionErrors*
- **GetSamplingRules**: *Retrieve all sampling rules* (to know what/when to send)
- **GetSamplingTargets** & **GetSamplingStatisticSummaries**: advanced
- The *X-Ray daemon needs to have an IAM policy* authorising the correct API calls to function correctly

### X-Ray Read APIs
- **GetServiceGraph**: *main graph*
	- ![[attachments/Pasted image 20220519160333.png]]
- **BatchGetTraces**: Retrieves a *list of traces specified by ID*. Each trace is a collection of segment documents that originates from a single request.
- **GetTraceSummaries**: Retrieves *IDs and annotations for traces* available for a specified time frame using an optional filter. *To get the full traces, pass the trace IDs to BatchGet Traces*.
	- We filter on annotations by using this.
- **GetTraceGraph**: Retrieves a *service graph for one or more specific trace IDs*.

## X-Ray with BeanStalk
- AWS Elastic Beanstalk platforms **include the X-Ray daemon**
- You can run the daemon by setting an option in the Elastic Beanstalk *console* or with a *configuration file* (in `.ebextensions/xray-daemon.config`)
- Make sure to give your *instance profile the correct lAM permissions* so that the X-Ray daemon can function correctly
- Then make sure your *application code is instrumented with the X-Ray SDK*

## X-Ray with ECS
> [!important]- Things to be done for using the *Side Car pattern*.
> - *Port mapping*: container port *2000* and the protocol **UDP**
> - *Environment Variable*: So that X-Ray SDK can find the X-Ray daemon.
> - *Link the containers* from the network standpoint.
> ---
> ![[attachments/Pasted image 20220519161509.png]]
> Because of linking the containers value of `xray-daemon` can be resolved.

> [!tip]- When using X-Ray with ECS we can **only** deploy x-ray **as a container**. 
> Depending on the ECS type it can be either daemon or side car.
> ---
> A Developer is deploying an application using Docker containers running on the Amazon Elastic Container Service (ECS). The Developer is testing application latency and wants to capture trace information between the microservices. Which solution will meet these requirements?
> Create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to the Amazon ECS cluster.

### ECS Cluster with EC2
- X-Ray **Container as a Daemon**
	- ![[attachments/Pasted image 20220519161055.png]]
- X-Ray **Container as a Side Car**. Also known as *multi container* setup.
	- ![[attachments/Pasted image 20220519161102.png]]

### ECS Cluster with Fargate
- Since we don't have control over the underlying instances we **can only use the Side Car pattern**.
	- ![[attachments/Pasted image 20220519161112.png]]