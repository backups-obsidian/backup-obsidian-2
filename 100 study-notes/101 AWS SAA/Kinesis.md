---
created: 2022-04-25 16:24
updated: 2023-02-16 09:44
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/Kinesis DVA | Kinesis DVA]]

---

- It is used to **ingest real time** data such as *application logs*, *Metrics*, *IoT telemetry*, *clickstreams* data etc.
- It is of **4 types**.
-  **Kinesis Video Streams**: capture, process and store video streams.

## Kinesis Data Streams
- **Capture**, **process** and **store** data streams.
	- ![[attachments/Pasted image 20220520154307.png]]
- It is **real time** (200 ms) .
- In kinesis data streams we have *streams* which are **made up of multiple shards**.
- The **more shards** you have in a stream the **more throughput** you will have. 
- *We control the throughput and scaling*, it has to be provisioned by configuring the number of shards.
- Each shard can **ingest** **1MB/s** or **1000Msg/s** and can give and **output** of **2MB/s**. If you get `ProvisionedThroughputExceeded` exception you should add more shards.
	- If want to go for a more *cost effective option* then you should use **batch messages**.
- By default, the *2MB/second/shard output* is *shared between all of the applications consuming data from the stream*. You should use built in **enhanced fan-out** if you have *multiple consumers retrieving data from a stream in parallel*. With enhanced fan out developers can register stream consumers to use enhanced fan-out and *receive their own 2MB/second pipe of read throughput per shard*.

> [!tip]+ If you want to *increase the performance* then try to *increase the number of shards* or go for *enhanced fan out*. 
> - **Enhanced fan out** is used when you are getting performance hit because of **multiple consumers**. 
> - Using enhanced fan-out will not help address the `ProvisionedThroughputExceeded exception` as the constraint is the capacity limit of the Kinesis Data Stream.
> - One solution is to do exponential backoff.

- *Billing* is based on *per shard provisioned*.
- We have a **retention period** of **1 day (default)** to **365 days**. Because of this we can reprocess the data. **Replay**.

> [!question]- An Amazon Kinesis Data Stream has recently been configured to receive data from sensors in a manufacturing facility. A consumer EC2 instance is configured to process the data every 48 hours and save processing results to an Amazon RedShift data warehouse. Testing has identified a large amount of data is missing. A review of monitoring logs has identified that the sensors are sending data correctly and the EC2 instance is healthy. What is the MOST likely explanation for this issue?
> *Records are retained for 24 hours* in the Kinesis Data Stream by **default**.

-  Once the *data* is *inserted* into Kinesis it **cannot be deleted (immutability)**.

- Data is ordered using **partition keys**. Data that shares the *same partition goes to the same shard (ordering)*. So partition key decides to which shard the data will go.
	- ![[attachments/Pasted image 20220520193307.png]]
	- We can have *5 (max) consumers in parallel* which means 5 MB/s of data.
	- If you would like to *scale the number of consumers to a large number* then using *SQS ordering over kinesis ordering* is a better choice. For better understanding read [[../102 AWS DVA/Kinesis DVA#SQS vs Kinesis Ordering|SQS vs Kinesis Ordering]]

> [!caution] Data is ordered at the shard level

> [!question]- A Developer has setup an Amazon Kinesis Data Stream with 6 shards to ingest a maximum of 2000 records per second. An AWS Lambda function has been configured to process these records. In which order will these records be processed?
> Lambda will receive each record in the exact order it was placed into the shard. There is *no guarantee of order across shards*.

### Producers
- *AWS SDK*
- *Kinesis Producer Library* (KPL)
- *Kinesis Agent*:
	- Standalone Java application that offers an **easy way** to collect and send data to Kinesis Data Streams.
	- It does a lot of things like log file rotation, retry upon failures, pre process the records before sending etc. 

> [!tip] Ease of usage: **Kinesis Agent** > KPL > AWS SDK.

> [!question]- A monitoring application that keeps track of a large eCommerce website uses Amazon Kinesis for data ingestion. During periods of peak data rates, *the producers are not making best use of the available shards*.   What step will allow the producers to *better utilise the available shards* and increase *write throughput* to the Kinesis data stream?
> Install the Kinesis Producer Library (**KPL**) *for ingesting data* into the stream.

### Consumers
- *Write your own*: 
	- Kinesis Client Library (KCL) 
	- AWS SDK
- *Managed*: 
	- Kinesis Data Firehose 
	- Kinesis Data Analytics

---
- Data is **base64 encoded**.
- Kinesis data streams takes care of **deduplication** and **ordering**.
- It is *NOT serverless* whereas kinesis firehose is. When you need to load data into something in a serverless fashion go for firehose.

> [!note] Keywords: 
> - *real-time*: **KDF is near real time so if the question mentions realtime then go for KDS**.
> - *replay* (data retention in days)
> - *deduplication*
> - *clickstream*
> - *downstream systems*

## Kinesis Data Firehose
- **Load streaming data** into AWS data stores and analytics tools.
	- ![[attachments/Pasted image 20220425192119.png]]
	- Remember the destinations by heart. *3* (**S3**, **ElasticSearch**, **Redshift**) + *1* + *1*

- **Commonly firehose reads** from **kinesis data streams**.
- **Writing** always takes place in **batches**.
- It is a **fully managed** service with **automatic scaling** and is **serverless**.
- We *pay for the data going through firehose*.
- It is **near real time**
	- Kinesis Data Firehose *buffers incoming records before delivering them to your S3 bucket*.
	- *Buffer interval*: *60 seconds* latency minimum for *non full batches*
	- *Buffer size*:  minimum 32 MB of data at a time
	- Record delivery is triggered once the value of either of the specified buffering hints is reached.

- Can be used for **transforming data** (using a *lambda*) (minimal overhead) coming from different sources.
- It can send **all or failed data to S3**.
- It doesn't have any data storage so no replay capability.

> [!question]- Why use KDS with Kinesis Data Firehose?
> *Kinesis Data Streams cannot directly write the output to S3*. In addition, KDS does not offer a plug and play integration with an intermediary Lambda function like Firehose does. You will need to do a lot of custom coding to get the Lambda function to process the incoming stream and then reliably dump the transformed output to S3.

> [!note] Keywords: **processing data**, *least infra maintenance (serverless)*, *load*, **store**, *data stores*.

> [!tip]+ Amazon Kinesis Data Firehose is the easiest way to capture, transform, and *load streaming data into Redshift* for **near real-time analytics**. It is also an *auto-scaling solution* as there is no need to provision any shards like Kinesis Data Streams.
> If you see **autoscaling** or *no infra management* keywords then always go for *Kinesis Firehose over Kinesis data streams*.

## Kinesis Data Analytics
- **Analyse** data streams with **SQL** or Apache Flink
- It is **fully managed** with **automatic scaling**.
- It is **serverless** offering.
- We *pay for the data that flows through*.
- It is used for **Real Time analytics**
- **Sources** can be either *KDS* or *KDF*.
	- ![[attachments/Pasted image 20220520192604.png]]
