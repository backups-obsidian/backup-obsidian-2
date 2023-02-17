---
created: 2022-05-16 12:39
updated: 2023-02-17 12:23
---
---
**Links**: [[102 AWS DVA Index]]

---
## Local Secondary Index
- **Alternative Sort Key** for your table (**same Partition Key** as that of base table)
- The Sort Key consists of one scalar attribute (String, Number, or Binary)
- *Up to 5 Local Secondary Indexes* per table
- **Must be defined at table creation time**.
	- To remember this remember global can be created any time hence global.
- *Attribute Projections* - can contain *some or all the attributes of the base table* (KEYS_ONLY, INCLUDE, ALL)

> [!example]- Example:
> ![[attachments/Pasted image 20220525143703.png]]
> - We can do a query on `user_id` or `game_id` but we cannot do a query on other attributes.
> - To do so we will have to do a *scan and then client side filtering*.
> - If we need to query based on `user_id` and `game_TS` then we need to create a LSI and define the LSI on `game_TS`.

## Global Secondary Index
- **Alternative Primary Key** (*HASH - partition key* or *HASH+RANGE - partition key + sort key*) from the base table
	- This means we can have *different partition and sort keys*. In LSI we could only change the sort key.
- *Speed up queries on non-key attributes*
- The Index Key consists of scalar attributes (String, Number, or Binary)
- *Attribute Projections* - *some or all the attributes* of the base table (KEYS_ONLY, INCLUDE, ALL)
- **Must provision RCUs & WCUs for the index**
- **Can be added/modified after table creation**

> [!example]- Example: 
> ![[attachments/Pasted image 20220525144407.png]]
> - In the example given above we can query based on `user_id` but we cannot query based on `game_id`
> - We need to do a scan and then do client side filtering.
> - Instead we can choose to *create a GSI*.

> [!question]- Question
> You have an application running for over a year now using a DynamoDB table, with Provisioned RCUs and WCUs, without any throttling issues. There's a requirement for your table to support second type of queries, so you have decided to use the existing Local Secondary Index (LSI) and create a new Global Secondary Index (GSI) to support these queries. One month later, the *table begins to experience throttling issues*. After checking the table's CloudWatch metrics, *you found that you haven't exceeded the table's Provisioned RCU and WCU*. What should you do?
> 
> The GSI is throttling so you need to provision more RCU and WCU to the GSI. *Global Secondary Index (GSI) uses an independent amount of RCU and WCU and if they are throttled due to insufficient capacity, then the main table will also be throttled*.
> To avoid potential throttling, the provisioned write capacity for a global secondary index should be equal or *greater than the write capacity of the base table* since *new updates will write to both the base table and global secondary index*.

> [!question]- A company is building an application to track athlete performance using an Amazon DynamoDB table. Each item in the table is identified by a partition key (user_id) and a sort key (sport_name). The table design is shown below: Partition key: `user_id`, Sort Key: `sport_name`, Attributes: `score`, `score_datetime`. A Developer is asked to write a leaderboard application to display the top performers (`user_id`) based on the score for each `sport_name.` What process will allow the Developer to extract results MOST efficiently from the DynamoDB table?
> Create a *global secondary index* with a *partition key of sport_name* and a *sort key of score*, and get the results.
> ---
> In this case **BOTH the partition key and sort key must be different** which means a Global Secondary index is required. *Partition key: sport_name*, *Sort Key: score*.

## Throttling
- Global Secondary Index (GSI):
	> [!caution]- **If the writes are throttled on the GSI, then the main table will be throttled** even if the WCU on the main tables are fine
	- *Choose your GSI partition key carefully*!
	- *Assign your WCU capacity carefully*!
- Local Secondary Index (LSI):
	- Uses the *WCUs and RCUs of the main table*
	- **No special throttling considerations**