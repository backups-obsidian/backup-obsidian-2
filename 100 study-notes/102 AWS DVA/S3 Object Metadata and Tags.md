---
created: 2023-03-01 08:50
updated: 2023-03-01 08:59
---
---
**Links**: [[102 AWS DVA Index]]

---
## User Defined Metadata
- When we *upload* an object we can assign metadata.
	- Metadata is just a fancy name for **key-value pairs** *attached to the object*.
- User-defined metadata names must begin with **`x-amz-meta-`**
- Amazon S3 stores user-defined metadata keys in lowercase.
- Metadata *can be retrieved while retrieving the object*.
- Sample metadata:
	- ![[attachments/Pasted image 20230301085409.png]]

## Object Tags
- **Key-value pairs** for objects in Amazon S3 
- Useful for *fine-grained permissions* (only access specific objects with specific tags)
- Useful for analytics purposes (using S3 Analytics to group by tags)

> [!caution]- We **CANNOT search** objects by *metadata or tags*.
> To search by either metadata or tags we must *create an index in a database like DynamoDB*.
> ![[attachments/Pasted image 20230301085802.png]]