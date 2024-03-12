---
created: 2024-03-11 12:00
updated: 2024-03-12 12:25
---
---
**Links**: [[121 MySQL for Developers Index]]

---
## Queries
- **The number one rule for writing good queries is using our indexes**.

### Using `EXPLAIN`
- `EXPLAIN` is a *statement provided by MySQL that helps us analyze how queries are executed in a database*. 
	- It shows the execution plan for a query, providing information about how the database will access tables and perform operations for the query.
	- Using `explain`: `EXPLAIN SELECT * FROM people;`
- Let's break down the columns in the output to understand what's going on:
	- **ID**: A unique identifier for the query being executed.
	- **Select Type**: Tells us the type of select statement is being executed. This can be simple, primary, union, or a few others.
	- **Table**: The name of the table being accessed.
	- **Partitions**: Displays the partitions being accessed for the query.
	- **Type**: The kind of access MySQL used to retrieve the data. *This is one of the most important column values*.
	- **Possible Keys**: The *possible indexes that MySQL could use*.
	- **Key**: The *actual index that MySQL uses*.
	- **Key Length**: Displays the length of the index used by MySQL.
	- **Ref**: The value being compared to the index.
	- **Rows**: An *estimated number of rows that MySQL needs to examine to return the result*.
	- **Filtered**: The estimated percentage of rows that match the query criteria.

#### `EXPLAIN` access type
- **`const`**: This access method is *one of the most efficient*. 
	- `Const` access is only used when a *primary key or unique index is in place*, allowing MySQL to locate the necessary row with a single operation. 
	- When you see `const` in the type column, it's telling you that MySQL knows there is only one match for this query, making the operation as efficient as possible.
	- Example: `select * from people where id=10`: Here `id` is the primary key of table.
- **`ref`**: `Ref` access is used when the **query includes an indexed column that is being matched by an equality operator**.
	- Example: `select * from people where first_name = "test"`: Here we have an index on the `first_name`.
- **`range`**: When you use `range` in the where clause, MySQL knows that it will need to look through a range of values to find the right data. 
	- MySQL will use the B-Tree index to traverse from the top of the tree down to the first value of the range. 
	- From there, MySQL *consults the linked list at the bottom of the tree to find the rows with values in the desired range*. 
	- It's essential to note that MySQL will examine every element in the range until a mismatch is found.
	- Example: `select * from people where id<10`: Here `id` is the primary key of table.
- **`index`**: The `index` access method indicates that **MySQL is scanning the entire index** to locate the necessary data.
	- Example: `select first_name from people where first_name != "test"`: Here we have an index on the `first_name`.
- **`all`**: `all` access method **means that MySQL is scanning the entire table to locate the necessary data**. 
	- `All` is the slowest and least efficient access method, so it's one that you want to avoid as much as possible.

> [!note] If you see `const` or `ref` access methods, you likely have a well-structured database that performs well. If you see `index` or `all` access methods, it might be time to investigate ways to optimize your database structure or indexing strategy.

### Index Obfuscation
- Imagine you have a table full of movies, and you want to filter out movies that are under two hours long. The length of each movie is in minutes, so you need to divide the length column by 60 to convert it to hours before running your query. However, by doing this, you've obfuscated your column and made it difficult for MySQL to use your index.
- To understand this better, let's look at how to avoid index obfuscation. 
	- First, *always leave your column alone as much as possible*! Any changes you make to it, such as dividing or combining it with other columns, make it more difficult to use an index effectively. 
	- Instead, *move everything to the other side of the operator when possible*.
	- In our case, instead of dividing the length column by 60, we should multiply it by 60 on the other side of the operator. By doing this, we allow the MySQL engine to calculate the result once and compare it against the index more efficiently.

```sql fold title:"Index obfuscation example"
-- query not using index
SELECT * FROM film WHERE length / 60 < 2;

-- query using index
SELECT * FROM film WHERE length < 2 * 60;
```

