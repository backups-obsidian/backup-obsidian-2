---
created: 2022-04-19 16:22
updated: 2023-03-18 08:47
---
---
**Links**: [[101 AWS SAA Index]]

---
## CloudWatch Metrics
- It **provides metrics for every service** in AWS
- **Metric** is a **variable** to monitor (eg: CPU Utilisation, Network in/out)
- Metrics belong to **namespaces**.
	- These namespaces were created automatically.
	- Namespaces are isolated from each other. 
	- We can create *custom namespaces*.
- **Dimension** is an **attribute of a metric (columns)** (instance id, environment, etc...).
	- We can use dimensions to *segment our metrics*. Like instance.id or environment.name.
- Example of dimensions:
	- ![[attachments/Pasted image 20230306084629.png]]
	- ![[attachments/Pasted image 20230306084714.png]]

> [!question]- A Developer is *publishing custom metrics* for Amazon EC2 using the Amazon CloudWatch CLI. The Developer needs to *add further context to the metrics being published by organising them by EC2 instance and Auto Scaling Group*. What should the Developer add to the CLI command when publishing the metrics using `put-metric-data`?
> Use `--dimensions` parameter.
> ---
> In **custom metrics**, the `--dimensions` parameter is common. A dimension *further clarifies what the metric is* and what data it stores.
> ![[attachments/Pasted image 20230216093036.png]]

- Metrics have **timestamps**.
- We can create CloudWatch **dashboard from metrics**.
- Metrics exist **only in the Region** in which they are created.

> [!question]- A Development team has deployed several applications running on an Auto Scaling fleet of Amazon EC2 instances. The Operations team have asked for a *display that shows a key performance metric for each application on a single screen for monitoring purposes*. What steps should a Developer take to deliver this capability using Amazon CloudWatch?
> **Create a custom namespace** with a unique metric name for each application.

## EC2 Monitoring
- We get EC2 instance metrics every **5 minutes**.
- We can get **detailed monitoring** at a **cost** where we get metrics every **1 minute**.
	- Detailed monitoring can be useful for *scaling ASGs faster*.

- List of things that are **monitored**: 
	- ***CPU utilisation***: CPU utilisation + **credit usage/balance**
	- *Network utilisation*: Network In/Out
	- **Disk Reads/Writes**: Only for **instance store**.
		- *If we have an EBS volume then the disk reads and writes will be 0 (will not be populated) in the graph*.
	- *Volume Reads/Writes*: For **EBS volume**.
	- *Status checks*
		- **Instance status**: check the *EC2 VM*
		- **System status**: check the *underlying hardware*

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

- To do this we use API call **`put-metric-data`**. 
- We can define the *resolution of custom metrics* using the **StorageResolution** API. There are two possible value:
    - **Standard** custom metric: *1 minute (60 seconds)*. Values pushed every 1 minute.
    - **High Resolution** custom metric: *1/5/10/30 second(s)* **Higher cost**

> [!question]- A Developer is configuring an Amazon ECS Service with Auto Scaling. The tasks should scale based on user load in the previous *20 seconds*. How can the Developer enable the scaling?
> Create a *high-resolution custom* Amazon CloudWatch metric for user activity data, then publish data *every 10 seconds*.
> ---
> Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data *every 5 seconds is incorrect* as the resolution is higher than required which will *cost more*. We need the resolution to be 20 seconds so that means publishing in 10 second intervals with 2 data points. At 5 second intervals there would be 4 data points which will incur additional costs.

> [!important]- For getting custom metrics we need to install CloudWatch agent on EC2.
> Behind the scenes the agent also uses `PutMetricData` API.

> [!important] CloudWatch Accepts metric data points **two weeks in the past** and **two hours in the future** (make sure to configure your EC2 instance time correctly)

> [!note] To remember *Normal Metrics*: **Detailed (1 m)**, *Custom Metrics*: **Resolution (1 s)**, *Alarms*: **Resolution (10 s)**

> [!question]- A telecommunications company that provides internet service for mobile device users maintains over 100 c4.large instances in the us-east-1 region. The EC2 instances run complex algorithms. The manager would like to track CPU utilisation of the EC2 instances as frequently as every 10 seconds.
> *Create a high-resolution custom metric and push the data* using a script triggered every 10 seconds.
> A high resolution custom metric has to be created since detailed monitoring can only happen at 1 minute intervals.

## Dashboards
- We can create custom dashboards for quick access to key metrics and alarms.
- **Dashboards are global**.
- Dashboards **can include graphs from different AWS accounts and regions**
	- This can be done by creating a *cross-account cross-Region dashboards*, which summarise your CloudWatch data from multiple AWS accounts and multiple Regions into one dashboard.
- Dashboards **can be shared with people who don't have an AWS account** (public, email address, 3rd party SO provider through Amazon Cognito)
- Pricing:
    -   **3 dashboards** (up to 50 metrics) for **free**
    -   **$3**/dashboard/month afterwards
- In a dashboard **all the metrics** will be shown in the **same time frame**.

## CloudWatch Logs
- This is the *best place* to **store logs** in AWS.
- We group the logs in **log groups**. 
	- The name of the group can be anything but is generally related to the application.
-   **Within** each **log group** we have **log streams** which represents *instances within the application/log files/containers*.
-   We can define a **log expiration policy** (never, 30 days etc). We are **paying for log storage** on CloudWatch logs.
- **CloudWatch Logs Insights** enables us to interactively search and *analyse your log data in Amazon CloudWatch Logs*. 
	- You *can perform queries* to help you quickly and effectively respond to operational issues.
	- If an issue occurs, you *can use CloudWatch Logs Insights to identify potential causes* and validate deployed fixes.
	- Using this we can analyse the logs with **minimal effort**.
	- CloudWatch Logs Insights *can be used add queries to CloudWatch Dashboards*.

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

> [!note]- Since we are interacting with CloudWatch logs and SSM parameter store we need to make sure the *permissions are correct*.
> This means we need to have correct IAM permissions attached to the EC2 instance role or to the access keys if we are using the unified agent on premises.

### Unified CloudWatch agent
- **Default namespace** for metrics collected by the Unified CloudWatch agent is **`CWAgent`** (can be configured/changed)
- We can collect metrics and **monitor system utilisation of individual processes** using the **`procstat` plugin**.
	- Supports both Linux and Windows servers
	- Example: *amount of time the process uses CPU*, amount of memory the process uses etc.
- Select which processes to monitor by
	- `pid file`: name of process identification number (*PID*) files they create
	- `exe`: *process name* that match string you specify (RegEx)
	- `pattern`: command lines used to start the processes (RegEx)
- Metrics collected by `procstat` plugin **begins with `procstat` pref** (e.g., procstat_cpu_time, procstat_cpu_usage, etc)
- You can set up the *CloudWatch agent to use multiple configuration files*. 
	- For example, you can use a *common configuration* file that collects a set of metrics and logs that you always want to collect from all servers in your infrastructure. 
	- You can then use *additional configuration files* that collect metrics from certain applications or in *certain situations*.
	- Any *configuration files appended* to the configuration must have **different file names** from each other and from the initial configuration file. 
	- If you use `append-config` with a configuration file with the *same file name* as a configuration file that the agent is already using, the append command **overwrites the information** from the first configuration file instead of appending to it. 
		- This is true even if the two configuration files with the same file name are on different file paths.

## CloudWatch Alarms
- When you create an alarm, you specify *three settings* to enable CloudWatch to evaluate when to change the alarm state:
	- **Period**: is the *length of time to evaluate the metric or expression* to create each individual data point for an alarm. It is expressed in *seconds*. If you choose one minute as the period, there is one datapoint every minute.
	- **Evaluation Period**: is the *number of the most recent periods*, or data points, to evaluate when determining alarm state.
	- **Datapoints to Alarm**: is the *number of data points within the evaluation period* that must be breaching to cause the alarm to go to the `ALARM` state. 
- Triggering in general *Evaluation period* and *datapoints to alarm*.
 
- They are used to **trigger notification from any metric**.
- Alarm has **3** states:
    - **OK**: not triggered
    - **Insufficient data**
    - **Alarm**: threshold has been breached and the alarm will be triggered.

- Period is the **length of time in seconds to evaluate the metric.** 
	- It can be **high resolution** custom metric: **10 sec**, **30 sec** or multiples of **60 sec**
- The alarms have **3 main targets**. 
	- *EC2* : *Stop*, *Terminate*, *Reboot*, or **Recover** an EC2 Instance. You can directly do these things from CloudWatch alarms, there is no need to use any other services like Lambda in between.
		- The following things will be **recovered**: 
			- Same *Private*, **Public**, *Elastic IP*, *metadata*, *placement group*, *EBS volume* 
			- *Instance store will not be recovered*.
	- *Auto scaling* : Trigger Auto Scaling Action
	- *SNS* : Send notification to SNS (from which *you can do pretty much anything*)  

- We can *test the alarm using the CLI*. This is useful if we want to trigger an alarm before its threshold to see if it results in proper actions.
	- Use `set-alarm-state` in CLI

### Composite Alarms (SOA)
- CloudWatch *Alarms are on a single metric*.
- Composite Alarms are *monitoring the states of multiple other alarms*.
- **AND** and **OR** conditions.
- Helpful to reduce "alarm noise" by *creating complex composite alarms*.
	- We can create complex alarms like if the CPU is high and the memory usage is high then alert me.
	- ![[attachments/Pasted image 20230306090342.png]]

### Service Quotas with CloudWatch Alarms (SOA)
- Notify you when you're close to a service quota value threshold
- Create **CloudWatch Alarms on the Service Quotas console**
	- Example: Lambda concurrent executions
		- ![[attachments/Pasted image 20230306093627.png]]
- *Helps you know if you need to request a quota increase* or shutdown resources before limit is reached
- It can be used with Trusted Advisor but it is not as good as using it with Service Quotas.

> [!question]- Which AWS services help you to be notified when your service quotas thresholds are approaching?
> AWS *Service Quota* & AWS *Trusted Advisor*.

## Synthetics Canary (DVA, SOA)
- **Configurable script** that *monitor your APIs, URLs, Websites*, etc.
	- Scripts written in *Node.js* or *Python*.
- Reproduce what your customers do programmatically to find issues before customers are impacted.
- Checks the *availability* and *latency* of your endpoints and can store load time data and *screenshots of the UI*.
- Integration with *CloudWatch Alarms*.
	- ![[attachments/Pasted image 20230301101509.png]]
	- If there is some problem then the cloudwatch alarms trigger a lambda which then updates the route53 DNS record to point to a healthy application. 
- Programmatic access to a *headless Google Chrome browser*.
- Can run once or on a *regular schedule*.
- We can use some existing **blueprints**.
	- Heartbeat Monitor: load URL, store screenshot and an HTTP archive file
	- *API Canary*: *test basic read and write functions of REST APIs*
	- Broken Link Checker: check all links inside the URL that you are testing
	- Visual Monitoring: compare a screenshot taken during a canary run with a baseline screenshot
	- Canary Recorder: used with CloudWatch Synthetics Recorder (record your actions on a website and automatically generates a script for that) 
	- GUI Workflow Builder: verifies that actions can be taken on your webpage (e.g., test a webpage with a login form)