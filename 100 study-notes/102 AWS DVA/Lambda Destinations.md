---
created: 2022-05-24 09:38
updated: 2023-02-16 08:36
---
---
**Links**: [[102 AWS DVA Index]]

---
## Destinations
- In case of **asynchronous invocations** and event mappers it was very hard for us to see if they had failed or succeeded.
- The idea is to send the *result of an asynchronous invocation* or *failure of an event mapper* somewhere.

### Asynchronous Invocations
- For asynchronous invocations we can define destinations for **both successful and failed events**. 
	- ![[attachments/Pasted image 20220524093950.png]]
- *Destinations* for asynchronous invocations are:
	- *SQS*
	- *SNS*
	- Lambda
	- EventBridge bus

> [!note] AWS *recommends you use destinations instead of DLQ now* (but both can be used at the same time)
> - They are newer
> - *DLQs* only allows you to *only send failures* of asynchronous invocations to SQS or SNS

### Event Source Mapping
- For **discarded event batches**
	- ![[attachments/Pasted image 20220524094431.png]]
- *Destinations*:
	- *SQS*
	- *SNS*
- In case of failure instead of blocking the entire batch we send them to destinations

> [!note] If you have lambda reading from an SQS queue you can either set up a DLQ or setup a destination.