---
created: 2022-05-25 12:27
updated: 2022-06-09 15:06
---
---
**Links**: [[102 AWS DVA Index]]

---
- For different capacity modes read [[../101 AWS SAA/DynamoDB#Read and Write Capacity Modes|capacity modes]].
## WCU Calculation
- One Write Capacity Unit (WCU) represents **one write per second for an item up to 1 KB** in size.
- If the items are larger than KB, more WCUs are consumed
- Example calculations: 
	- We write 10 items per second, with item size 2 KB - `2 * 10 = 20 WCUs`
	- We write 6 items per second, with item size 4.5 KB - `6 * 5 = 30 WCUs` (4.5 *gets rounded to the upper KB*)
	- We write 120 items per minute, with item size 2 KB - `4 WCUs`

## Strongly Consistent vs Eventually Consistent Reads
- When we write to DynamoDB we write to one of the servers and our data is replicated to other servers for redundancy.
- There is chance that we wrote to server 1 and now we are trying to read from server 2.
	- ![[attachments/Pasted image 20220525120751.png]]
- *Eventually Consistent Read* (**default**)
	- If we read just after a write, it's possible **we'll get some stale data** because of replication
- *Strongly Consistent Read*
	- If we read just after a write, we will **get the correct data**
	- Set `ConsistentRead` parameter to **True** in API calls (GetItem, BatchGetItem, Query, Scan)
	- Consumes **twice the RCU**.
	- **slightly higher latency**.

### RCU Calculation
- **One** Read Capacity Unit (RCU) represents *one Strongly Consistent Read per second*, or *two Eventually Consistent Reads per second*, for an item up to **4 KB** in size
- If the *items are larger than 4 KB*, more RCUs are consumed *rounded up to the next multiple of 4 KB*.
- Example:
	- 10 Strongly Consistent Reads per second, with item size 4 KB: `10 RCUs`
	- 16 Eventually Consistent Reads per second, with item size 12 KB: `24 RCUs`
	- 10 Strongly Consistent Reads per second, with item size 6 KB: `20 RCUs` (6KB is rounded to 4KB)

## DynamoDB Partitions Internal
- **Data is stored in partitions**
- Partition Keys go through a *hashing algorithm* to know to which partition they go to
	- ![[attachments/Pasted image 20220525121609.png]]

> [!note]- WCUs and RCUs are **spread evenly across partitions**.
> If you have 10 partitions and you provision 10 WCUs and 10 RCUs then they are going to be spread evenly across partitions. This means each partition is going to get 1 WCU and 1 RCU.

> [!question]- An application begins to receive a lot of `ProvisionedThroughputExceededException` exceptions from a DynamoDB table that you manage. After checking the CloudWatch metrics, you found that you haven't exceeded the *total provisioned RCU*. What is a possible cause for this?
> You have a *Hot Partition/Hot Key*. Remember RCUs and WCUs are spread across all the table's partitions.

## Throttling
- If we exceed provisioned RCUs or WCUs at the partition level, we get `ProvisionedThroughputExceededException`.
- **Reasons**:
	- Hot Keys: *one partition key is being read too many times* (e.g., popular item)
	- Hot Partitions
	- *Very large items*, remember *RCU and WCU depends on size of items*
- **Solutions**:
	- *Exponential backoff* when exception is encountered (*already in SDK*)
	- *Distribute partition keys* as much as possible
	- If *RCU issue*, we can use DynamoDB Accelerator (*DAX*)

