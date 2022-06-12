---
created: 2022-05-16 12:39
updated: 2022-06-07 15:30
---
---
**Links**: [[102 AWS DVA Index]]

---
## Kinesis Data Streams
### Capacity Modes
- **Provisioned mode**:
	- You choose the number of shards provisioned, scale manually or using API
	- Each shard gets *I MB/s in* (or 1000 records per second)
	- Each shard gets *2MB/s out* (classic or enhanced fan-out consumer)
	- You *pay per shard provisioned per hour*

- **On-demand mode**:
	- No need to provision or manage the capacity
	- Default capacity provisioned (*4 MB/s in or 4000 records* per second)
	- **Scales automatically** based on *observed throughput peak during the last 30 days*
	- Pay per stream per hour & data in/out per GB

> [!note] If you don't know capacity then go for on-demand.

### Security
- KDS is *deployed within our VPC*.
	- ![[attachments/Pasted image 20220520154912.png]]
- Control access/authorisation using |AM policies
- Encryption *in flight using HTTPS* endpoints
- Encryption *at rest using KMS*
- You can implement encryption/decryption of data on client side (harder)
- **VPC Endpoints available for Kinesis to access within VPC**
- Monitor API calls using CloudTrail

### Kinesis Producers
- Puts data records into data streams
- **Data record** consists of:
	- **Sequence number** (*unique per partition-key within shard*) 
	- **Partition key** (must specify while put records into stream)
	- **Data blob** (up to 1 MB)
- **Producers**: Only **3**
	- *AWS SDK*: simple and custom producer
	- *Kinesis Producer Library (KPL)*: KPL is **built on top of SDK** but it has some advanced capabilities as APIs like batch, compression, retries
	- *Kinesis agent*: It is **built on top of KPL** and is used monitor log files and stream those to KDS.
- API to send data to kinesis is called the `PutRecord` API.

> [!note] **Use batching** with `PutRecords` API to *reduce costs* & *increase throughput*

> [!important]- Partition keys are **hashed** to decide which shard data should be sent. We decide the partition key.
> - If one of the devices is very chatty it might overwhelm the chart
> - Use **highly distributed partition key** to avoid hot partition. For example using browsers as partition keys is not a good choice since a lot of people use Google Chrome this would overwhelm a particular shard.
> ---
> ![[attachments/Pasted image 20220520160144.png]]

- Solutions for `ProvisionedThroughputExceeded`
	- Use **highly distributed partition key**
	- Retries with *exponential backoff*
	- *Increase shards* (scaling)
	- ![[attachments/Pasted image 20220520160349.png]]

### Kinesis Consumers
- **Consumers**: **5**
	- *AWS Lambda*
	- Kinesis Data *Analytics*
	- Kinesis Data *Firehose*
	- Custom Consumer (*AWS SDK*): **Shared/Classic or Enhanced Fan-Out**
	- Kinesis Client Library (*KCL*): library to simplify reading from data stream

#### Classic vs Enhanced Fan-Out
![[attachments/Pasted image 20220520160852.png]]

- *Shared/Classic Fanout*:
	- **Pull** model
	- Low number of consuming applications
	- Read throughput: *2 MB/sec per shard across all consumers*
	- Latency ~200 ms
	- **Minimise cost** ($)
	- Consumers **poll data** from Kinesis using `GetRecords` API call

- *Enhanced Fanout*:
	- **Push** model
	- Multiple consuming applications for the same stream
	- *2 MB/sec per consumer* per shard
	- Latency ~70 ms. *Lower latency since push model*.
	- **Higher costs** 
	- Kinesis pushes data to consumers over **HTTP/2** (`SubscribeToShard` API)
	- *Soft limit of 5 consumer applications (KCL)* per data stream (default)

#### Lambda
![[attachments/Pasted image 20220520161620.png]]
- Supports Classic & Enhanced fan-out consumers 
- **Read records in batches**
- Can *configure batch size* and *batch window*
- If *error* occurs, *Lambda retries* until succeeds or data expired
- Can process *up to 10 batches per shard simultaneously*

#### KCL
- A **Java library** that helps read record from a Kinesis Data Stream with distributed applications sharing the read workload
- Each shard is to be read by only one KCL instance
	- **4 shards = max. 4 KCL instances**
	- 6 shards = max. 6 KCL instances
- *Progress is check pointed* into **DynamoDB (needs lAM access)**
	- ![[attachments/Pasted image 20220520163055.png]]
	- ![[attachments/Pasted image 20220520163134.png]]
- *Track other workers and share the work amongst shards using DynamoDB* 
- KCL can run on *EC2*, *Elastic Beanstalk*, and *on-premises*
- *Records are read in order* at the shard level
- Versions:
	- KCL 1.x (supports shared consumer)
	- *KCL 2.x* (**supports shared & enhanced fan-out consumer**)

### Shard Splitting & Merging
- **Shard Splitting**: Used to *scale up*
	- Used to increase the Stream capacity (1 MB/s data in per shard)
	- Used to **divide a hot shard**
		- ![[attachments/Pasted image 20220520163433.png]]
	- The *old shard is closed and will be deleted* once the data is expired
	- *No automatic scaling* (manually increase/decrease capacity)
	- *Can't split into more than two shards in a single operation*
	- This **increases the capacity and cost**.

- **Shard Merging**: Used to *scale down*
	- Decrease the Stream capacity and save costs
	- Can be used to **group two shards** with low traffic (**cold shards**)
		- ![[attachments/Pasted image 20220520163655.png]]
	- *Old shards are closed and will be deleted* once the data is expired
	- *Can't merge more than two shards in a single operation*
	- **Decreases capacity and cost**.

## SQS vs Kinesis Ordering
- Let's assume 100 trucks, 5 kinesis shards, SQS FIFO
- **Kinesis Data Streams**:
	- On average you'll have 20 trucks per shard
	- Trucks will have their data ordered within each shard
	- *The maximum amount of consumers in parallel we can have is 5* since we have 5 shards.
	- *Can receive up to 5 MB/s of data*
- **SOS FIFO**:
	- You only have *one SQS FIFO queue*
	- You will have *100 Group ID*
	- You can have up to **100 Consumers** (*due to the 100 Group ID*)
	- You have up to 300 messages per second (or *3000 if using batching*)

> [!important] If you want to have a **dynamic number of consumers** then it is better to use *SQS FIFO*. If you have **lots of data** and want ordering within the shard use *Kinesis*.