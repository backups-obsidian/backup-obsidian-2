---
created: 2022-04-25 14:13
updated: 2022-05-19 21:08
---
---
**Links**: [[101 AWS SAA Index]]
**Keywords**: *notification*

---
## SNS
- It is used when we want to send **one message to many receivers**.
- For example a buying service can send a message to the buying service, fraud service etc. This is cumbersome since every time we have to **add a new receiver** we have to code that integration. Instead we should prefer to use the **pub/sub model**. The buying service will publish the message on a **SNS topic** and all the receivers will be able to subscribe to that topic.
	- ![[attachments/Pasted image 20220425150211.png]]

- In SNS the **event producer** sends message to **only** **one SNS topic**. There is only one producer and **many consumers**(also known as listeners/subscriber).
- Each **subscriber** to the topic will get **all the messages**. Although there is an option to filter the messages.
- We can have upto 12M subscriptions on a topic.
- We can have upto **100K topics** in SNS.
- Different **subscribers**
	- **AWS Services**:
		- *Lambda*
		- *SQS*
		- *Kinesis Data Firehose*
	- Other:
		- *Email*
		- *SMS*
		- *Mobile Notifications*
		- HTTP/HTTPS
- Many services can **send data directly to SNS for notifications**. 
	- Like CloudWatch alarms
	- *S3 bucket events*.

> [!caution] In SNS *data is not persisted* so if not delivered then data is lost.

## How to Publish
- **Topic Publish** (*using SDK*):
	- Create a topic
	- Create a subscription (or many)
	- Publish to the topic

- **Direct Publish** (for *mobile apps SDK*)
	- Create a platform application
	- Create a platform endpoint
	- Publish to the platform endpoint
	- Works with Google GCM, Apple APNS, Amazon ADM...

## Security 
- Security is exactly same as [[SQS#Security|SQS Security]]
- It also uses **access policies (resource based policies)**. [[SQS#Access Policies Resource based policies|Same effect as SQS]]

## SQS + SNS fan out
- We want a **message** to be **sent to** **multiple SQS queues**. If we send it individually to all the queues then there can be problems like application crashes or delivery failures.
- We use the fan out pattern. **Push** the **message in SNS topic** and **multiple queues** will **subscribe** to this **topic**.
	- ![[attachments/Pasted image 20220425153917.png]]

- **Fully decoupled** model and there is **no data loss**.
- SQS will give us data persistence, delayed processing and retries for work.
- We can *easily add more SQS queues* as subscribers over time.

> [!note] For this to work we need to make sure that *SQS access policy allows write from SNS*

> [!question]- Use case of *fanout: S3 Events*
> For the same combination of event type (eg: object create) and prefix (eg: /images) we can create only one S3 event rule. With this we can only use a single SQS queue. *To use multiple queues we use fan out pattern*.
> ---
> ![[attachments/Pasted image 20220519210144.png]]

> [!important]- *SNS to S3* via Kinesis Data Firehose
> This is possible since SNS can directly send data to KDF.
> ![[attachments/Pasted image 20220519210420.png]]

## SNS FIFO
- The topic name must *end with* `.fifo`
- **Subscribers** can **only** be **SQS FIFO** queues.
- We get similar features to SQS FIFO like *ordering* (group ID) and *deduplication*.
- This is useful if we need **fanout** + **ordering** + **deduplication**
- *Limited throughput* (same throughput as SQS FIFO)

## SNS Message Filtering
- **JSON policy** to **filter messages** sent to SNS topic’s **subscriptions**.
	- ![[attachments/Pasted image 20220425161929.png]]
- If the subscription *doesn’t have a filter policy it will receive every message*. We can have it like this and filter the messages at the application level but it is unnecessary wastage of resources.
