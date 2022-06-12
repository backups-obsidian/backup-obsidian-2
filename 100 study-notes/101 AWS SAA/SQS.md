---
created: 2022-04-25 14:13
updated: 2022-06-09 14:38
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/SQS DVA | SQS DVA]]
**Keywords**: *3000 msgs*, *FIFO (only once)*, *manual message deletion*

---
- It is a simple queuing service in which we have *multiple producers and multiple consumers*.
- Oldest AWS service and **fully managed**. 
- **Scales infinitely**.

> [!question]- How do you do prioritisation of work using queues?
> AWS recommends *using separate queues* to provide prioritisation of work.
> Create two SQS standard queues: one for pro and one for lite. Set up *EC2 instances to prioritise polling for the pro queue over the lite queue*.

- There are *2 types of queue*.

## Types of Queues
### Standard Queue
- **Unlimited throughput** and **messages** in queue
- **Default** retention period of messages is **4** days and **maximum** is **14** days. After 14 days they are **automatically deleted**.
- Low latency (<10 ms)
- **Size limit** of the messages is **256KB**.
- We **can have duplicate messages** in the queue and this has to be taken care at the application level.
- We have **out of order messages**. Best effort ordering is attempted by AWS.
- Producers *send message to SQS* using the **SDK**.
- The message will *persist in the queue* until the **consumer deletes it** or the *retention period is exceeded*.
	- If the consumer doesn't delete the message after processing then a single message will be *processed multiple times*.
- The **consumers poll** for the messages and then process it. They can *receive upto 10 messages at a time*.
- We can auto *scale ASGs based on the queue length*. There is no out of the box metric from SQS or EC2 or to do the scaling we have to create a custom metric.

### FIFO Queue
- It stands for First in first out. First message arriving in the queue will be the first message leaving the queue.
- Since we have the order constraint this SQS queue has a **limited throughput**. **300 Msg/s** **without batching** and **3000 Msg/s with**. This happens when you have *10 (maximum) batches each of 300 Msg/s*.
- We have **exactly one send capability**. There will be **no duplicates**. The duplicates are removed.
- Messages are **processed in order** by the consumer.

> [!caution]- The queue name has to end with `.fifo` 
> This means if you have an existing standard queue you cannot convert it to a fifo queue. You will have to *create a new fifo queue*.

#### Ordering data in SQS FIFO
- If you don’t use **group Id** then you can only have one consumer.
- For *multiple consumers you need group Id*. This is similar to Partition key in [[Kinesis]]. 

## Security
### Encryption
- **In flight encryption** using HTTPS
- At **rest encryption using KMS**
	- To store data in encrypted queues we should enable SQS KMS encryption
	- Messages can be encrypted in both standard and FIFO queues.
- We can also have client side encryption.

### Access Controls
- We have **IAM policies** to regulate access to the SQS API.

### Access Policies / Resource based policies
- We also have **SQS Access Policies** (similar to S3 bucket policies). These are useful for
    - **cross account access**: allowing other accounts to poll for messages.
	    - ![[attachments/Pasted image 20220519201824.png]]
    - **allow other services** (SNS, S3 etc) to **write to SQS queue**.
		- ![[attachments/Pasted image 20220519201851.png]]

> [!note] Access policies have to be used for other services to write to SQS. IAM roles is just for controlling the API.

## Message Visibility Timeout
- When a **message is polled** by a consumer it **becomes invisible** to other consumers.
- The **default** value of visibility timeout is **30s**. This means once the message has been polled by one of the consumers it *becomes invisible to other consumer for 30s*.
- If the **message is not processed within the visibility timeout** then it will be visible to other consumers and they can pick up this message.
- So we see that if the *message is not processed within the visibility timeout window* then it will be **processed twice** by two different consumers or the same consumer.
- So if the **consumer knowns** that it is going to **need more time** than the visibility period to process the message then the consumer can call `ChangeMessageVisibility` API.
- If the visibility timeout is **HIGH** and the *consumer crashes* then *reprocessing* will *take time*. If the visibility timeout is **LOW** then there is a *chance of messages* getting *processed* **multiple times**.

## Dead Letter Queue
- Let us consider a scenario where the consumer crashes while processing the message. The message will return back to the queue and other consumer will pick it to process the message. Now the other consumer also fails. This keeps on happening again and again. This means there is something wrong with the message.
- We can set a **threshold** to how many times this can happen. This is known as the **MaximumReceives** threshold. If the **threshold** is **exceeded** it goes into **Dead Letter Queue** (DLQ)
- The DLQ will contain the message for processing. It is useful for **debugging** to understand what is causing the consumers to crash.

> [!note] Messages in DLQ are in a standard queue so *they will expire*. It is recommended to **set maximum (14) expiration time** for DLQs.
- *DLQ is nothing but a standard queue assigned as DLQ for the main queue*. So when you edit the main queue you have the option for enabling DLQ and then providing a standard queue that will act DLQ.

## Delay Queue
- It is used to **delay messages** so that the consumers don’t see it immediately upto **15 minutes**.
- By **default delay** is **0s** i.e. the *messages are available immediately* to be processed by a consumer.
- We can set a **delay at the queue level** to delay **all** the **messages** coming to the queue **or** every time a producer sends a message we can set a **per message delay** using the `DelaySeconds` parameter.

## Long Polling
- When a consumer requests for a message and if there are **no messages** in the queue then the consumer can **optionally wait** for the messages. This is known as **Long Polling**.
- It **eliminates empty responses**.
- It is done to **decrease latency** and the **number of API calls** to SQS. 
- Long polling *increases the efficiency of your application*.
- The wait time is **1** to **20s. 20s is preferred**. The parameter is called `ReceiveMessageWaitTimeSeconds`. By **default** it is **0** which means **short polling**. Any value greater than 0 means long polling.
- Long polling can be **configured at the queue level** **OR** any **consumer** can configure it using the `WaitTimeSeconds` API.

## Request Response System
- The producer sends messages to the Request Queue with the instruction on which queue the responders should place the response payload.
- To implement this pattern use **SQS Temporary Queue Client**. It uses **virtual queues** instead of deleting/creating SQS queues which is cost effective. We **don’t** have to **worry about implementation details**.
- We have a scalable backend with many requests and responses without overwhelming the machines.