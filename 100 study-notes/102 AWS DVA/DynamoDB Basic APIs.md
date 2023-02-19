---
created: 2022-05-16 12:39
updated: 2023-02-19 09:48
---
---
**Links**: [[102 AWS DVA Index]]

---
## Writing Data
- `PutItem`
	- Creates a **new item** or **fully replace an old item** (*same Primary Key*)
	- *Consumes WCUs*

- `UpdateItem`
	- *Edits an existing item's attributes* or *adds a new item if it doesn't exist*
		- Like **Upsert**.
	- Can be used to implement **Atomic Counters** a numeric attribute that's *unconditionally incremented*.
		- With an atomic counter, the numeric value will increment each time you call `UpdateItem`. 
	
- `ConditionalWrites`
	- By *default*, the DynamoDB write operations (PutItem, UpdateItem, DeleteItem) are *unconditional*.
	- Accept a *write/update/delete* (`PutItem/UpdateItem/DeleteItem`) only if conditions are met, otherwise returns an error.
	- Helps with **concurrent access** to items
	- **Prevents overwriting**.

> [!note]- APIs for writing data: *`PutItem`, `UpdateItem ` and `DeleteItem`*.

## Reading Data
- `GetItem`
	- Read **based on Primary key**
	- **Gets only a single item**.
	- Primary Key can be HASH or HASH+RANGE
	- *Eventually Consistent Read (default)*
	- Option to use **Strongly Consistent Reads** (*more RCU*, *might take longer*, *higher latency*)
		- For consistent reads Set the `ConsistentRead` parameter to true when calling `GetItem`.
	- **ProjectionExpression** (`--projection-expression`):  can be specified to **retrieve only certain attributes (columns)**
		- A projection expression is *a string that identifies the attributes you want*. 
		- To retrieve a single attribute, specify its name. 
		- For multiple attributes, the names must be comma-separated.

### Query
- Query **returns items (rows)** based on:
	- **KeyConditionExpression**: (`--query`)
		- *Partition Key value* (must be `=` operator): **required**
		- *Sort Key value* (=, <, <=, >, >=, Between, Begins with): **Optionally**, you can provide a sort key attribute and use a comparison operator to *refine the search results*.
	- **FilterExpression** (`--filter-expression`)
		- Additional filtering *after the Query operation* (before data returned to you)
		- Use *only with non-key attributes* (does not allow HASH or RANGE attributes)
- *Returns*:
	- The number of items specified in **Limit**
	- Or up to **1MB of data**
- To get more data we need to use pagination.
- Can *query table*, a *Local Secondary Index*, or *Global Secondary Index*.

### Scan
> [!note]- `GetItem` was *for one item*, *query was for specific partition key* and *scan is to read out an entire table* and then filter our data. It is **inefficient**.
> Filtering has to be done on the client side

- Returns up to 1 MB of data use pagination to keep on reading
- **Consumes a lot of RCU**
- Limit impact using **Limit** or reduce the size of the result and pause
- For *faster performance*, use **Parallel Scan**
	- *Multiple workers* scan multiple data segments at the same time
	- Increases the throughput and RCU consumed
	- Limit the impact of parallel scans just like you would for Scans
- Can use *ProjectionExpression* & *FilterExpression* (no changes to RCU)

> [!question]- How to *scan* the whole table in the *shortest possible time* whilst ensuring the *normal workload is NOT affected*.
> Use the **Parallel Scan API** operation and **limit** the rate.

## Deleting Data
- `DeleteItem`
	- Delete an **individual item**
	- Ability to perform a conditional delete
- `DeleteTable`
	- Delete a *whole table and all its items*
	- **Much quicker deletion** than calling `DeleteItem` on all items

## Batch Operations
- Allows you to **save in latency** by *reducing the number of API calls*
	- *Reduces network overhead* of multiple transactions.
- Operations are done in **parallel** for better efficiency.
- Part of a batch can fail; in which case we need to try again for the failed items
- `BatchWriteItem`
	- Up to **25** `PutItem` and/or `DeleteItem` in one call
	- Up to **16 MB** of data written, up to 400 KB of data per item
	- **Can't update items** (use `UpdateItem`)

> [!caution]- We **DON'T** have batch operations for **updating items** (`UpdateItem`).

- `BatchGetItem`
	- Return *items from one or more tables*
	- Up to *100 items*, up to **16 MB** of data
	- Items are retrieved in *parallel to minimise latency*

> [!question]- An application needs to read up to *100 items* at a time from an Amazon DynamoDB. Each item is up to *100 KB* in size and all attributes must be retrieved. What is the BEST way to minimise latency?
> Use `BatchGetItem`.

## Returning WCU consumed
- For operations *`PutItem`, `UpdateItem` and `DeleteItem`*.
- To return the number of WCU consumed by any of these operations, set the *`ReturnConsumedCapacity`* parameter to one of the following:
	- `TOTAL` - returns the **total** number of write capacity units consumed.
	- `INDEXES` - returns the *total number of WCU consumed, with* **subtotals** for **the table and any secondary indexes** that were affected by the operation.
		- If you see the word **indexes in the question** then go for `INDEXES`. 
	- `NONE` - no write capacity details are returned. (This is the **default**.)