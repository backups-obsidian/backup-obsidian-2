---
created: 2024-03-10 12:19
updated: 2024-03-11 09:43
---
---
**Links**: [[121 MySQL for Developers Index]]

---
## Indexes
> [!important]  Without an index, MySQL will begin with the first row and then *read through the entire table* to find the relevant rows.

- **Indexes are the best way to ensure that the queries are performant**.
	- If the queries are performant then the application is performant since the database is generally the main bottleneck in any application.
- Important things about indexes:
	- An index is a *completely separate data structure*.
		- *So whenever the table is updated the index has to be updated*.
	- An index **maintains a copy of part of our data**.
		- An index takes part of the data from the main table and copies it into its own data structure.
	- An index has a *pointer which points back to the row of the table*.
		- This is necessary as an index just stores a part of the data from the table and the rest of the data is in the main table.

> [!note]- So the schema is driven by data and indexes are driven by queries.
> - We need to understand our queries (access patterns) to create good indexes.
> - Developers are the best people to create the indexes since they understand the access patterns of the application.

- **B+ tree is the underlying data structure behind an index**.
	- Leaf nodes contain the data in a B+ tree.

### Primary Keys
- Primary keys are *by default not nullable*.
- **An index is automatically created for the primary key**.
- A secondary key is nothing but an index on a column.
- *A primary key is also an index but it is a special kind of index*.

> [!caution]+ The leaf nodes of the index (B+ tree) of a primary key contain the data of the table. **The primary key index is the table**. It is also known as *clustered index*.
> - Secondary indexes don't contain the entire data.
> - *Primary keys determine how the data is stored on disk*.
> 
> ![[attachments/Pasted image 20240310131226.png]]
> Example of an index of the primary key of a table containing names and emails as columns. Notice that all the data is stored in the leaf nodes.

- When we create a *secondary key* (index) the *leaf nodes contain the value of the column on which it was created and a pointer to the primary key*.
- **When we look up a value using a secondary key, it traverses the secondary index key, finds the node uses the pointer ID to traverse through the primary index key to get all of the data**.

> [!note]- Use `UNSIGNED BIGINT AUTO_INCREMENT id` as the primary key. 
> There are other options too but this would work the best in most of the cases.

### Where to add indexes
- Here are some general rules of thumb to consider when deciding whether or not to use an index:
	- Do not assume that anything that shows up in the where clause of a query should have an index. Consider all queries being run and their respective access patterns.
	- **DO NOT create an index on every column**. This will slow down inserts by functionally duplicating your table. It also won't help reads as much as you'd hope.
	- *Do consider the entire query when deciding which columns to index. This includes sorting, grouping, and joining*.
	- Do not worry about trying to create the perfect index for every query. It may not always be possible, and sometimes you will have to rework the queries to take advantage of existing indexes.

```sql fold title:"Creating an index in MySQL"
alter table people add index birthday_index (birthday);

-- This creates an index named `birthday_index` on the `birthday` column of the `people` table.
```

### Index Selectivity & Cardinality
- *Cardinality refers to the number of distinct values in a particular column* that an index covers. 
- When you use the `SHOW INDEXES` command in MySQL, the `cardinality` column in the output shows you the approximate total number of unique values in a given index column.
- *Selectivity, on the other hand, refers to how unique the values in a column are*.
	- Selectivity can be calculated using:  `total number of distinct values  / total number of values`.
- It is a measure of *how selective an index can be in narrowing down results when queried*. 
	- **The higher the selectivity of an index, the better it is for optimizing query performance**.
	- The index on primary key is perfectly selective with a value of 1 since all the values are unique.
- *We can use the information on the selective value of a column to determine whether or not to put an index on it*.
	- Suppose we have a table with a `type` column that has two possible values, `user` and `admin`, with most rows having the value `user`. In this case, putting an index on the `type` column may not be the best approach to find rows with `type = "user"` since most of the rows contain the same value. The index is not highly selective for `user`, but it is highly selective for `admin`!
	- Furthermore, if we're stuck and can't figure out why an index isn't being used, we can check the selectivity of the index in question. If the selectivity is low, it is most likely that MySQL decided to read the table, assuming the index would be slower than a full table scan.

### Prefix Indexes
- When you have a long string column, such as an URL or a UID, its impossible to index it entirely. 
	- In that case, you can **index a part of the string by creating prefix indexes**.
- By indexing just a part of a string column, you can make the index much smaller and faster. 
	- For example, indexing only the first four or five characters of a string reduces the size of the index while potentially maintaining the selectivity.
	- Prefix indexing is especially suitable for long, evenly distributed, and unique strings, such as UUIDs and hashes.

```sql fold title:"Creating prefix indexes"
ALTER TABLE people ADD INDEX (first_name(5));

-- In the above statement, we're using the `ADD INDEX` syntax along with specifying the prefix length of five for the `first_name` column.
```

### Composite Indexes
- **Composite indexes cover multiple columns** instead of having individual indexes on each column.

```sql fold title:"Creating multi column index"
ALTER TABLE people ADD INDEX multi (first_name, last_name, birthday);
```

- When you inspect the indexes on the people table, you'll notice the **multi index with a sequence in the index column**. 
	- *This sequence indicates the order of the columns in the index*, which is crucial for how the index can be used.
	- ![[attachments/Pasted image 20240310150533.png]]
- *There are two main rules for using composite indexes*:
	- **Left-to-right, no skipping**: MySQL *can only access the index in order, starting from the leftmost column and moving to the right*. 
		- It can't skip columns in the index.
	- **Stops at the first range**: MySQL *stops using the index after the first range condition encountered*.

```sql fold title:"Understanding index usage"
-- We get what part of the index is being used using `key_len`

EXPLAIN SELECT * FROM people WHERE first_name = 'Aaron' AND last_name = 'Francis';
-- MySQL is using both the `first_name` and `last_name` parts of the index.

EXPLAIN SELECT * FROM people WHERE last_name = 'Francis' AND first_name = 'Aaron' ;
-- MySQL is not using any part of the composite index since last_name is infront of first_name.

EXPLAIN SELECT * FROM people WHERE first_name = 'Aaron' AND last_name = 'Francis' and birthday = '1989-02-14';
-- MySQL is using all the parts of the index

EXPLAIN SELECT * FROM people WHERE first_name = 'Aaron' AND last_name < 'Francis' and birthday = '1989-02-14';
-- MySQL stops using the index at the first range condition (last_name in this case) and doesn't use the birthday part of the index.
```

- Choosing the right order for columns in a composite index depends on the access patterns of your application. Consider the following tips when defining composite indexes:
	- *Equality conditions that are commonly used would be good candidates for being first in a composite index*.
	- Range conditions or less frequently used columns would be better candidates for ordering later in the composite index.

### Covering Indexes
- *Covering indexes are not a separate type of index in MySQL, but rather a special situation in which an index covers the entire set of requirements for a single query*. 
- A covering index is a regular index that *provides all the data required for a query without having to access the actual table*. 
	- When a query is executed, the database looks for the required data in the index tree, retrieves it, and returns the result. This eliminates the need for the engine to access the actual table, saving a secondary traversal to gather the rest of the data.
- **For an index to be considered a covering index, it must have all the data needed for a particular query**. 
	- This includes the *columns being selected*, the *columns being filtered on*, and the *columns being used for sorting*. 
	- If an index satisfies all of these requirements, it is said to be a "covering index" for _that_ query.
- For example if we have 