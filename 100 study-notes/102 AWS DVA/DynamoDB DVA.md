---
created: 2022-05-25 10:53
updated: 2023-02-17 11:43
---
---
**Links**: [[102 AWS DVA Index]]

---
## Partition keys
- Also known as **primary key**.
- Option 1: Partition Key (*HASH*)
	- Partition key must be **unique** for each item
	- Partition key must be **diverse** so that the data is distributed
	- Example: *User_ID for a users table*
		- ![[attachments/Pasted image 20220525105323.png]]

- Option 2: Partition Key + Sort Key (HASH + RANGE)
	- The **combination must be unique** for each item
	- Data is *grouped by partition key*
	- Example: *users-games table*, User_ID for Partition Key and Game_ID for Sort Key
		- ![[attachments/Pasted image 20220525105504.png]]

> [!question]- What is the best Partition Key to **maximise data distribution**? movie_id ,producer_name, leader_actor_name, movie_language?
> - *movie_id* has the **highest cardinality** so it's a good candidate 
> movie_language doesn't take many values and may be skewed towards English so it's not a great choice for the Partition Key

## PartiQL
- Use a **SQL-like syntax** to manipulate DynamoDB tables
- *Supports some* (but not all) statements:
- It supports Batch operations

## Optimistic Locking
- DynamoDB has a feature called **Conditional Writes**
- It is *type of concurrency model*.
- A strategy to *ensure an item hasn't changed before you update/delete it*
- Each item has an *attribute* that acts as a **version number**
	- ![[attachments/Pasted image 20220525150559.png]]

## DynamoDB Streams
- [[../101 AWS SAA/DynamoDB#DynamoDB Streams|DynamoDB Streams]]
- Architectures possible using DynamoDB streams:
	- ![[attachments/Pasted image 20220525151642.png]]
- Ability to choose the *information that will be written to the stream*:
	- `KEYS_ONLY`: only the key attributes of the modified item
	- `NEW_IMAGE`: the entire item, as it appears after it was modified
	- `OLD_IMAGE`: the entire item, as it appeared before it was modified
	- `NEW_AND_OLD_IMAGES`: both the new and the old images of the item
- DynamoDB Streams are *made of shards, just like Kinesis Data Streams*. This is the reason why KCL works with DynamoDB.
	- You don't provision shards, this is automated by AWS

> [!caution]- Records are **not retroactively populated in a stream** after enabling it 

### DynamoDB Streams & Lambda
- You need to define an **Event Source Mapping** to read from a DynamoDB Streams
	- ![[attachments/Pasted image 20220525152115.png]]
- You need to ensure the *Lambda function has the appropriate permissions*
- Lambda function is **invoked synchronously**

## DynamoDB TTL
- It is possible to *delete items in DynamoDB after a certain time automatically*.
	- ![[attachments/Pasted image 20220525155332.png]]
- Doesn't consume any WCUs (i.e., *no extra cost*) 
- The **TTL attribute must be a Number** data type with **Unix Epoch timestamp value**
- *Expired items* deleted within **48 hours** of expiration time.
- *Expired items*, that *haven't been deleted, appears* in reads/queries/scans (if you don't want them, filter them out)
- Expired items are *deleted from both LSIs and GSIs*
- A *delete operation for each expired item enters the DynamoDB Streams* (can help recover expired items)
- Use case: 
	- *deleting expired cookies of users* and then prompting them to login again.
	- reduce stored data by keeping only current items
	- adhere to *regulatory obligations*

## DynamoDB CLI
- `--projection-expression`: **one or more attributes** to retrieve. Retrieve a subset of attributes.
- `--filter-expression`: filter items before returned to you. *Filter happens on the client side*.
	- If we want the filtering to happen on server side we use a query.
- General AWS *CLI Pagination options* (e.g., DynamoDB, S3, ...)
	- `--page-size`: specify that AWS CLI **retrieves the full list of items** but with a *larger number of API calls* instead of one API call (default: 1000 items)
		- So suppose if there are 100 items in the table and you set `--page-size` to 25 then 4 API calls will be made to fetch the data.
	- `--max-items`: max number of items to fetch (returns `NextToken`)
		- If you want to retrieve only 25 items from a table of 100 items we use `--max-items`. 
		- ![[attachments/Pasted image 20220525160538.png]]
	- `--starting-token`: specify the last NextToken to *retrieve the next set of items*
		- Once we have the NextToken we use it as an argument to `--starting-token` along with `--max-items`
		- If we don't have any next token then this means we are at the end.
		- ![[attachments/Pasted image 20220525160838.png]]

> [!note]- For pagination we can either use `--page-size` or **use `--max-items` with `--starting-token`**.

> [!question]- A company has a large Amazon DynamoDB table which they scan periodically so they can analyse several attributes. The scans are consuming a lot of provisioned throughput. What technique can a Developer use to *minimise the impact of the scan* on the table's provisioned throughput?
> Set a **smaller page size** for the scan.

## DynamoDB Transactions
- Coordinated, **all-or-nothing operations** (add/update/delete) to multiple items across one or more tables.
	- ![[attachments/Pasted image 20220525162205.png]]
- Provides Atomicity, Consistency, Isolation, and Durability (**ACID**)
- *Read Modes*: Eventual Consistency, Strong Consistency, *Transactional*
- *Write Modes*: Standard, *Transactional*
- Consumes **2x WCUs & RCUs**
	- Since DynamoDB performs *2 operations* for every item (**prepare & commit**)
- The *two operations* performed by DynamoDB are: 
	- `TransactGetItems`: one or more `GetItem` operations
	- `TransactWriteItems`: one or more `PutItem`, `UpdateItem`, and `DeleteItem` operations
- Use cases: *financial transactions*, managing orders, *multiplayer games*
- **Capacity Computations**:
	- 3 Transactional writes per second, with item size 5 KB: `3*5*2 = 30WCUs`
	- 5 Transaction reads per second with item size 5 KB: `5*(8/4)*2 = 20WCUs`

## DynamoDB as Session State Cache
- It's common to use **DynamoDB to store session state**

> [!important]- DynamoDB vs ElastiCache for session state
> - If the question asks for **in-memory** solution then use **ElastiCache**
> - If the question asks for **serverless/auto-scaling** solution then go for **DynamoDB**

## DynamoDB Write Sharding
- Imagine we have a voting application with two candidates, candidate A and candidate B
- If Partition Key is Candidate_ID, this results into two partitions, which will generate issues (e.g., Hot Partition)
- A strategy that allows better distribution of items evenly across partitions 
- **Add a suffix to Partition Key value**
	- ![[attachments/Pasted image 20220525163538.png]]
- Two methods:
	- Shading Using *Random Suffix*
	- Shading Using *Calculated Suffix*

## DynamoDB Write Types
- There are 4 types of writes:
	- ![[attachments/Pasted image 20220525163833.png]]

## DynamoDB with S3
- Storing *large objects*:
	- ![[attachments/Pasted image 20220525165652.png]]
- *Indexing S3 objects metadata*:
	- ![[attachments/Pasted image 20220525165756.png]]

## DynamoDB Operations
- **Table Cleanup**:
	- Option 1: Scan + `DeleteItem`
		- *Very slow*, consumes RCU & WCU, *expensive*
	- Option 2: Drop Table + Recreate table
		- *Fast, efficient, cheap*
- **Copying a DynamoDB Table**
	- Option 1: Using *AWS Data Pipeline*
		- Uses an EMR cluster and S3 behind the scenes
	- Option 2: *Backup and restore* into a new table
		- Takes some time
	- Option 3: Scan + `PutItem` or `BatchWriteItem`
		- Write your own code

## Miscellaneous Features
- Encryption *at rest using AWS KMS* and *in-transit using SSL/TLS*
- In addition to encryption at rest, which is a *server-side encryption* feature, AWS provides the Amazon **DynamoDB Encryption Client**. 
	- This is a *client-side encryption* library that enables us to *protect your table data before submitting it to DynamoDB*.
- For backup and restore we have **Point-in-time Recovery (PITR)** just like RDS
- For migrating data to and from DynamoDB we use DMS.
- **Fine grained access control** for users:
	- Using Web Identity Federation or *Cognito Identity Pools*, each user gets AWS credentials
	- You can assign an lAM Role to these users with a **Condition** to *limit their API access to DynamoDB*
		- ![[attachments/Pasted image 20220525170645.png]]
	- **LeadingKeys limit row-level access** for users on the **Primary Key**

> [!question]- A Developer is creating a social networking app for games that uses a single Amazon DynamoDB table. All users’ saved game data is stored in the single table, but users should not be able to view each other’s data. How can the Developer *restrict user access* so they can only view their own data?
> Restrict access to specific items based on *certain primary key values* (*LeadingKeys*).
