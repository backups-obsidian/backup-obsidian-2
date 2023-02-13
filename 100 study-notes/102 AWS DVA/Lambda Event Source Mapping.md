---
created: 2022-05-23 17:00
updated: 2023-02-13 15:50
---
---
**Links**: [[102 AWS DVA Index]]

---
## Event Source Mapping
- Services:
	- **Kinesis Data Streams (KDS)**
	- **SQS & SQS FIFO queue**
	- **DynamoDB Streams**
- Records need to be **polled** from the source. This means lambda needs to ask the services for some records.
- Lambda is invoked **synchronously**.
- Internally an **event source map** is created which is responsible for polling kinesis and getting the results back.
- Once the event source map has data it will invoke the lambda function synchronously for processing.

> [!question]+ A company is setting up a Lambda function that will process events from a DynamoDB stream. The Lambda function has been created and a stream has been enabled. What else needs to be done for this solution to work?
> - An *event-source mapping* must be **created on the Lambda side** to associate the DynamoDB stream with the Lambda function.
> - The configuration of the event source mapping for stream-based services (DynamoDB, Kinesis), and Amazon SQS, is made on the **Lambda side**.

## Event Source Mappers
- There are two categories of event source mappers:
- Streams:  **Kinesis** & **DynamoDB streams**
	- ![[attachments/Pasted image 20220523194948.png]]
- SQS & SQS FIFO
	- ![[attachments/Pasted image 20220523200116.png]]

### Streams
#### Processing 
- An event source mapping creates an **iterator for each shard**, *processes items in order*
- Start with new items, from the beginning or from timestamp
- **Processed items aren't removed from the stream** (other consumers can read them)
- *Low traffic*: Use *batch window* to accumulate records before processing
- *High traffic*: You can *process multiple batches in parallel*
	- Up to *10 batches per shard*
	- *In-order processing* is still guaranteed for each partition key,
	- ![[attachments/Pasted image 20220523195510.png]]

#### Error Handling
- By default, if your function returns an *error*, the **entire batch is reprocessed** until the function succeeds, or the items in the batch expire.
- To ensure in-order processing, processing for the **affected shard is paused** until the error is resolved
- You can configure the event source mapping to:
	- Discard old events
	- Restrict the number of retries
	- Split the batch on error (to work around Lambda timeout issues)
- Discarded events can go to a [[Lambda Destinations|destination]].

### SQS & SQS FIFO
#### Processing
- Event Source Mapping will poll SQS (**Long Polling**) 
- Specify *batch size*
- Recommended: Set the *queue visibility timeout to 6x the timeout of your Lambda function*
- To use a **DLQ**
	- *Set-up on the SQS queue*, not Lambda (*DLQ for Lambda is only for async invocations* and here we are dealing with synchronous invocations)
	- Or use a Lambda destination for failures

> [!note] For the lambda to read messages from the queue its execution role must have SQS permission.

## Queues & Lambda
- Lambda also *supports in-order processing for FIFO* (first-in, first-out) queues, scaling up to the number of active message groups.
- For standard queues, items aren't necessarily processed in order.
- Lambda *scales up to process a standard queue as quickly as possible*.
- When an error occurs, batches are returned to the queue as individual items and might be processed in a different grouping than the original batch.
- Occasionally, the event source mapping might receive the same item from the queue twice, even if no function error occurred.
- **Lambda deletes items from the queue after they're processed successfully**.
- You can configure the source queue to send items to a dead-letter queue if they can't be processed.

## Lambda Event Mapper Scaling
- *Kinesis Data Streams & DynamoDB Streams*:
	- **One Lambda invocation per stream shard**
	- If you use parallelisation, up to *10 batches processed per shard* simultaneously
- *SQS Standard*:
	- Lambda adds 60 more instances per minute to scale up
	- Up to 1000 batches of messages processed simultaneously
- *SQS FIFO*:
	- Messages with the same GroupID will be processed in order
	- The Lambda function scales to the number of active message groups