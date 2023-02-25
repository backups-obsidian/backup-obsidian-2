---
created: 2023-02-24 15:14
updated: 2023-02-24 15:22
---
---
**Links**: [[102 AWS DVA Index]]

---
## Optimistic Locking
- It is **type of concurrency model**.
- A strategy to *ensure an item hasn't changed before you update/delete it*
- Each item has an *attribute* that acts as a **version number** or a *timestamp*.
	- ![[attachments/Pasted image 20220525150559.png]]

## Optimistic Locking vs Conditional Writes
- **Both are data consistency control mechanisms** with different ways of handling *concurrency*.

- **Conditional writes** allow you to *write data to a table only if certain conditions are met*. 
	- This can be useful in *scenarios where you want to avoid overwriting existing data*, or where you want to *ensure that a certain value has been updated before you write new data to the table*. 
		- For example, you can use a conditional write to update an item in a table only if a certain attribute has a specific value. If the attribute does not have the expected value, the write operation will fail.

> [!question]- A commercial bank is developing an online auction application with a DynamoDB database that will allow customers to bid for real estate properties from the comforts of their homes. The application should allow the minimum acceptable price established by the bank prior to the auction. The opening bid entered by the staff must be at least the minimum bid and the new bids submitted by the customers should be greater than the current bid. The application logic has already been implemented but the DynamoDB database calls should also be tailored to meet the requirements.
> Configuring the database calls of the application to *use conditional updates and conditional writes* with a *condition expression that will check if the new bid* submitted by the customer is greater than the current bid.

- **Optimistic locking is a concurrency control mechanism** that allows multiple clients to read and write to the same data without interfering with each other. 
	- When you perform an *update operation*, you include a *version number* or a *timestamp* with the data you want to update. 
	- When another client attempts to update the same data, DynamoDB checks the version number or timestamp to ensure that the data has not been modified by another client in the meantime. 
	- If the data has been modified, the update operation fails.

- So, while *conditional writes* allow you to ensure that *certain conditions are met* before writing data to a table, **optimistic locking helps to prevent conflicts** when *multiple clients* are attempting to update the same data. 

## Difference between Optimistic Locking and Pessimistic Locking
- **Optimistic locking** is a *locking mechanism* that **assumes** that **conflicts** between concurrent requests are **rare**, and so it *DOES NOT users from modifying the same item at the same time*. 
	- Instead, optimistic locking uses a **version number** to detect conflicts. 
	- When an item is read, the current version number is returned along with the item's contents. 
	- When an update is made, the version number is incremented. 
	- If two updates occur concurrently, one will fail because the version number will not match the version number stored in the database. 

> [!note] **Optimistic locking** is generally used in *situations* where *conflicts are infrequent* and the overhead of maintaining locks is not justified.

- **Pessimistic locking**, on the other hand, is a *locking mechanism* that assumes that *conflicts* between concurrent requests are **common** and so **it acquires locks** to prevent multiple users from modifying the same item at the same time.
	- With pessimistic locking, when *an item is read*, *a lock is placed* on the item to prevent other users from modifying it until the lock is released. 
	- Pessimistic locking is generally **used in situations where conflicts are frequent**, and the *overhead of acquiring locks is justified*.

- In DynamoDB, **optimistic locking is implemented using conditional writes**. 
	- When updating an item, a conditional expression can be used to ensure that the update only occurs if the version number stored in the database matches the version number supplied with the update request. 
	- If the version number does not match, the update request fails.

- **Pessimistic locking** in DynamoDB is implemented using the **`LockItem` operation**. 
	- When a user wants to modify an item, they **first acquire a lock** on the item using the `LockItem` operation. 
	- While the lock is held, other users are prevented from modifying the item. 
	- Once the user has finished modifying the item, they **release the lock using the `UnlockItem` operation**.

> [!important] The main difference between optimistic and pessimistic locking in DynamoDB is that *optimistic locking assumes conflicts are rare* and detects conflicts using a version number, while *pessimistic locking assumes conflicts are common* and prevents conflicts by acquiring locks.