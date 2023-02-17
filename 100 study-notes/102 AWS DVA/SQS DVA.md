---
created: 2022-05-16 12:39
updated: 2023-02-17 12:50
---
---
**Links**: [[102 AWS DVA Index]]

---
## Extended Client
- *How to send large messages*? The size limit on messages is 256 KB.
- We can upload the large message to S3 and use a *pointer* to the large message (*small metadata message*) as a message to SQS.
	- ![[attachments/Pasted image 20220519203156.png]]

## Must Know API
- `CreateQueue(MessageRetentionPeriod)`: Create a new queue with the specified retention time for messages. (*max 14 days*)
- `DeleteQueue`: delete the queue with the messages.
- `PurgeQueue`: delete all the messages in queue
- `SendMessage(Delay Seconds)`, `ReceiveMessage`, `DeleteMessage`
- `MaxNumberOfMessages`: default 1, **max 10** (for `ReceiveMessage` API)
	- To receive a *batch of messages*.
- `ReceiveMessageWaitTimeSeconds`: Long Polling
	- Or call the `ReceiveMessage` API with the `WaitTimeSeconds` parameter set to 20.
- `ChangeMessageVisibility`: change the message timeout
- Batch APIs for `SendMessage`, `DeleteMessage`, `ChangeMessageVisibility` helps **decrease your costs**.

## FIFO Advanced
### Message Deduplication
- De-duplication interval is **5 minutes**. This means that if you send the same message *twice within the 5 minutes* then the second message will be *refused*.
- **Two** de-duplication methods:
	- *Content-based deduplication*: will do a **SHA-256 hash** of the message body
		- ![[attachments/Pasted image 20220519204755.png]]
		- ![[attachments/Pasted image 20220519205238.png]]
	- Explicitly provide a **`MessageDeduplicationID`** in the `SendMessage` API.

> [!question]- An application collects data from sensors in a manufacturing facility. The data is stored in an Amazon SQS Standard queue by an AWS Lambda function and an Amazon EC2 instance processes the data and stores it in an Amazon RedShift data warehouse. A fault in the sensors’ software is causing *occasional duplicate messages to be sent*. Timestamps on the duplicate messages show they are generated within a few seconds of the primary message. How can a Developer prevent duplicate data being stored in the data warehouse?
> Use a FIFO queue and configure the Lambda function to add a *message deduplication token* to the message body.

### Message Grouping
- If you specify the *same value of `MessageGroupID` in an SQS FIFO queue*, you can **only have one consumer**, and *all the messages are in order*.
- To get **ordering at the level of a subset of messages**, specify *different values for MessageGroupID*
	- ![[attachments/Pasted image 20220519205145.png]]
	- *Messages that share a common `MessageGroupID` will be in order within the group*
	- *Each Group ID can have a different consumer* (**parallel processing**)
	- **Ordering across groups is not guaranteed**
- This is useful when you don't need ordering of all the messages but *want ordering of messages for a particular ID*.

> [!important] You can have **as many consumers as MessageGroupID** for your SQS FIFO queues.

> [!question]- You are using AWS SQS FIFO queues to get the ordering of messages on a per `user_id` basis. As a developer, which message parameter should you set the value of `user_id` to guarantee the ordering?
> `MessageGroupID`

## Miscellaneous
> [!question]- An Amazon Simple Queue Service (SQS) has to be configured between two AWS accounts for shared access to the queue. AWS account *A has the SQS queue* in its account and AWS account *B has to be given access to this queue*.
> - The account A administrator creates an IAM role and attaches a permissions policy—that grants permissions on resources in account A—to the role.
> - The *account A administrator attaches a trust policy to the role that identifies account B as the principal* who can assume the role.
> - The account *B administrator delegates the permission to assume the role to any users in account B*. This allows users in account B to create or access queues in account A.