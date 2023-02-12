---
created: 2022-04-19 16:22
updated: 2023-02-10 10:50
---
---
**Links**: [[101 AWS SAA Index]]

---
## CloudWatch Metrics
- It **provides metrics for every service** in AWS
- **Metric** is a **variable** to monitor (eg: CPU Utilisation, Network in/out)
- Metrics belong to **namespaces**.
	- These namespaces were created automatically.
- **Dimension** is an **attribute of a metric** (instance id, environment, etc...).
	- We can use dimensions to segment our metrics. Like instance.id or environment.name.
- We can choose upto **10 dimensions per metric**.
- Metrics have **timestamps**.
- We can create CloudWatch **dashboard from metrics**.
- Metrics exist **only in the Region** in which they are created.

## EC2 Monitoring
- We get EC2 instance metrics every **5 minutes**.
- We can get **detailed monitoring** at a **cost** where we get metrics every **1 minute**.
	- Detailed monitoring can be useful for *scaling ASGs faster*.

- List of things that are **monitored**: 
	- ***CPU utilisation***
	- *Network utilisation*
	- *Disk performance*
	- *Disk Reads/Writes*

- Predefined metrics for *target tracking* policy.
	- `ALBRequestCountPerTarget`
	- `ASGAverageCPUUtilization` 
	- `ASGAverageNetworkOut` 

### Custom Metrics
- Since a lot of important metrics are not published. We can define our **custom metrics** in CloudWatch.  ^b7481a
	- ***memory (RAM)*** 
	- *Disk space/utilisation*  
	- *Disk swap* 
	- *Number of logged in users*
	- *processes*

- To do this we use API call `PutMetricData`. 
- We can define the *resolution of custom metrics* using the **StorageResolution** API. There are two possible value:
    - **Standard** custom metric: *1 minute (60 seconds)*. Values pushed every 1 minute.
    - **High Resolution** custom metric: *1/5/10/30 second(s)* **Higher cost**

> [!important]- For getting custom metrics we need to install CloudWatch agent on EC2.
> Behind the scenes the agent also uses `PutMetricData` API.

- Important: CloudWatch Accepts metric data points **two weeks in the past** and **two hours in the future** (make sure to configure your EC2 instance time correctly)

> [!note] To remember *Normal Metrics*: **Detailed (1 m)**, *Custom Metrics*: **Resolution (1 s)**, *Alarms*: **Resolution (10 s)**

> [!question]- A telecommunications company that provides internet service for mobile device users maintains over 100 c4.large instances in the us-east-1 region. The EC2 instances run complex algorithms. The manager would like to track CPU utilisation of the EC2 instances as frequently as every 10 seconds.
> *Create a high-resolution custom metric and push the data* using a script triggered every 10 seconds.
> A high resolution custom metric has to be created since detailed monitoring can only happen at 1 minute intervals.

## Dashboards
- We can create custom dashboards for quick access to key metrics and alarms.
- **Dashboards are global**.
- Dashboards can include graphs from **different AWS accounts and regions**
- Dashboards **can be shared with people who don't have an AWS account** (public, email address, 3rd party SO provider through Amazon Cognito)
- Pricing:
    -   **3 dashboards** (up to 50 metrics) for **free**
    -   **$3**/dashboard/month afterwards
- In a dashboard **all the metrics** will be shown in the **same time frame**.

## CloudWatch Logs
- This is the *best place* to **store logs** in AWS.
- We group the logs in **log groups**. The name of the group can be anything but is generally related to the application.
-   **Within** each **log group** we have **log streams** which represents instances within the application/log files/containers.
-   We can define a **log expiration policy** (never, 30 days etc). We are **paying for log storage** on CloudWatch logs.
- **CloudWatch Logs Insights** enables us to interactively search and *analyse your log data in Amazon CloudWatch Logs*. 
	- You *can perform queries* to help you quickly and effectively respond to operational issues.
	- If an issue occurs, you *can use CloudWatch Logs Insights to identify potential causes* and validate deployed fixes.
	- Using this we can analyse the logs with **minimal effort**.

> [!important]+ By *default* log expiration policy is **Never Expire**.
> *Log retention policy* is defined at the *log group level*.

### CloudWatch Logs Filter
- CloudWatch Logs **can use filter expressions**. These metric filters can then be used to *trigger CloudWatch alarms*. 
	- ![[attachments/Pasted image 20220519133638.png]]
- Essentially you are **creating a metric** by filtering a specific pattern from the logs. Since alarms can only be triggered from metrics this is the reason why we are able to create alarms from CloudWatch logs by filtering for metrics.
- Examples 
	- *Find a specific IP inside* of a log
	- Or count occurrences of "ERROR" in your logs. If there are too many occurrences then trigger an alarm.

> [!caution] Filters **do not retroactively filter data**. Filters only publish the metric data points for *events that happen after the filter was created*.

### Logs can be sent to
- *Amazon S3* (exports): 
	- **not real time** can take upto *12 hours*.
	- API call of `CreateExportTask`
- **Subscription filters**: 
	- **real time**
	- We can use *Kinesis Data Streams*, *Kinesis Data Firehose*, *AWS Lambda* with subscription filters.
	- Using subscription filters we can *send logs to S3 in near real time*.
	- ![[attachments/Pasted image 20220427210509.png]]
	- Using subscription filters we can do *log aggregation from multiple accounts and multiple regions*.
		- ![[attachments/Pasted image 20220427210631.png]]

### Logs for EC2
- We need an agent to push cloud watch logs from an EC2 instance. **By default EC2 instance won’t push logs**.
- IAM role of writing to CloudWatch logs should be attached to the EC2 instance.

- To collect logs from your Amazon *EC2 instances* and **on-premises servers** into Cloud Watch Logs, AWS offers  
	- a new unified CloudWatch agent,  
	- an older CloudWatch Logs agent. 

- It is *recommended to use the unified CloudWatch agent* since it has the following **advantages**:
	- You can **collect both logs and [[CloudWatch#^b7481a|Advanced Metrics]]** with the installation and configuration of *just one agent*.
	- We can do centralised configuration for all our unified agents using the SSM parameter store.
	- With *unified agent* we can *get a lot more metrics with more granularity*.
	- The unified agent enables the collection of logs *from servers running Windows Server*.

## CloudWatch Alarms
- They are used to **trigger notification from any metric**.
- Alarm has **3** states:
    - **OK**: not triggered
    - **Insufficient data**
    - **Alarm**: threshold has been breached and the alarm will be triggered.

- Period is the **length of time in seconds to evaluate the metric.** It can be **high resolution** custom metric: **10 sec**, **30 sec** or multiples of **60 sec**
- The alarms have **3 main targets**. 
	- *EC2* : *Stop*, *Terminate*, *Reboot*, or **Recover** an EC2 Instance. You can directly do these things from CloudWatch alarms, there is no need to use any other services like Lambda in between.
		- The following things will be **recovered**: 
			- Same *Private*, **Public**, *Elastic IP*, *metadata*, *placement group*, *EBS volume* 
			- *Instance store will not be recovered*.
	- *Auto scaling* : Trigger Auto Scaling Action
	- *SNS* : Send notification to SNS (from which *you can do pretty much anything*)  

- We can *test the alarm using the CLI*. This is useful if we want to trigger an alarm before its threshold to see if it results in proper actions.
	- Use `set-alarm-state` in CLI
