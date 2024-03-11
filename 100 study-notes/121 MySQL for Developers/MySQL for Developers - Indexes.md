---
created: 2024-03-10 12:19
updated: 2024-03-11 12:13
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
	- **Left-to-right, no skipping**: MySQL *can only access the index in order, starting from the leftmost column and moving to the right*.  ^a5b81c
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
- For example if we have a table where there is a composite index with `first_name`, `last_name` and `birthday` then the following query will use a covering index
	- `select first_name from people where first_name = 'Some Value' `

> [!question]- How do covering indexes work?
> - In MySQL, the *primary key is a clustered index*, which means that the data is physically sorted in the order of the primary key.
> - A secondary index, on the other hand, is a separate data structure that maintains a copy of part of the data. When you create a composite index, MySQL creates a B-tree on the specified columns to index them. 
> - **This B-tree contains a copy of the indexed columns and a pointer to the corresponding row in the clustered index**.
> - When a query is executed, the engine traverses the B-tree to find the required rows and then follows the pointer to fetch the corresponding rows from the clustered index. 
> - However, *with a covering index, the engine can retrieve all the data it needs from the secondary index itself, without having to access the clustered index*.
> - This is possible because a covering index includes all the columns required for the query and does not need to access the clustered index to retrieve additional data.

- Because a covering index must include _all_ of the columns required for a query, it can be challenging to find a covering index in the real world.
	- Indexes can have a significant impact on database performance, and an excessive number of indexes can lead to slower performance. It may or may not make sense to try to design for a covering index.

### Functional Indexes
- Function-based indexes are **used in cases where you need to create an index on a function rather than a column**.
- For example, consider a scenario where you need to retrieve all the records where the birth month is February. 
	- You might have a `birthday` column in your table and an index on it, but applying a function such as `month()` to it will not allow you to use the index.
- Functional indexes allows you to search for records based on the results of that function, while still taking advantage of the speed and efficiency benefits of an index.

```sql fold title:"Creating functional indexes"
-- sample table
CREATE TABLE people (
  id INT(11) NOT NULL AUTO_INCREMENT,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  birthday DATE NOT NULL,
  PRIMARY KEY (id)
);

-- We are creating an index called `idx_month_birth` on the results of the `MONTH(birthday)` function. The double parentheses are used to denote that we are creating a function-based index.
ALTER TABLE people ADD INDEX idx_month_birth ((MONTH(birthday)));
```

### Indexing JSON columns
- *MySQL CANNOT index JSON columns*.
	- Instead, you have to **find a way to index a specific key inside the JSON blob**.
- *Using generated columns* of indexing a specific key in a JSON column:
	- Lets suppose we are indexing for the `email` key in the JSON column.
	- We first alter the table to add a new generated column. We'll call it `email` and make it `VARCHAR(255)`.
	- We then index the new `email` column.

```sql fold title:"Using generated columns for indexing key in a JSON column"
-- creating the generated column
ALTER TABLE json_data ADD COLUMN email VARCHAR(255) GENERATED ALWAYS AS (`json` ->> '$.email');

-- indexing on the generated column
ALTER TABLE json_data ADD INDEX (email);
```

### Indexing for wild card searches
- Let's say we want to search for all rows where the email column starts with the name "aaron." 
- We can use the `LIKE` operator and the `%` wildcard character to find all matches: `SELECT * FROM people WHERE email LIKE 'aaron%';`
- *If we have an index on `email`, MySQL will use that index in the above query*.
- If we want to find all rows where the email column contains the word "aaron" anywhere within the column: `SELECT * FROM people WHERE email LIKE '%aaron%';`.
	- **Since we have a wildcard character at the beginning of the search string, MySQL CANNOT use the index we created on the email column**. 
	- This means the query will be slower than if we had used a wildcard character only at the end of the search string.

> [!note]- MySQL cannot use an index when a wildcard character is at the beginning of a search string.
> If its something trivial then one solution is to create a generated column and then index it.

### Full text indexes
- While B tree indexes work well for wildcard searches at the end of a search string, they may not be sufficient for more complex text searches.
- **Full text indexes allow us to search for specific words or phrases within a larger text column with much greater efficiency than using a simple wildcard search**.
- Full-text indexing and searching in MySQL can be a powerful tool for searching across multiple columns and finding specific words or phrases within text blocks.
- *While it may not be as robust as a standalone search engine, it's a great option if you're already using MySQL as your database and want to avoid adding another tool to your infrastructure*.

### Invisible Indexes
- *Sometimes you might need to drop an index in MySQL, but you're not entirely sure of the ramifications*.
	- This is where making an index invisible in MySQL comes in handy.
- Making an index invisible allows you to monitor how your queries perform without the index without having to rebuild it. 
	- If everything goes well, you can drop the index. 
	- But if something goes wrong, you can quickly turn the index back on without any hassle.

### Duplicate Indexes
- Suppose we have a table called `people`, and we create two indexes:

```sql fold title:"Two indexes"
ALTER TABLE people ADD INDEX first_name (first_name);

-- composite index
ALTER TABLE people ADD INDEX full_name (first_name, last_name, birthday);
```

- We know that the `full_name` index covers the `first_name` index, since the first key part of the `full_name` index is `first_name`. 
	- *Therefore, the `first_name` index is redundant and can be safely removed*.

> [!caution] When we add an index on a column in InnoDB (engine behind MySQL), we're really adding an index on `column_name` and `id`. Similarly, when we add an index on multiple columns, we're actually adding an index on `column_1_name`, `column_2_name`, `column_3_name`, and `id`. This is because _InnoDB always appends the primary key to the leaf nodes_ of each index.

- Case where the `first_name` index is not redundant: 
	- Consider the query: `SELECT * FROM people WHERE first_name = 'Aaron' ORDER BY id DESC;`
	- MySQL will use the `full_name` index for the access pattern, *but it will have to manually sort the rows because the `id` column is all the way at the end ([[MySQL for Developers - Indexes#^a5b81c| left to right no skipping]]) of the index*. 
	- This can have a negative impact on performance, especially for large tables.

### Foreign Keys & Foreign key constraints
- A **foreign key is a column or set of columns in a table that references the primary key of another table**. 
	- This enables related data to be linked together in separate tables. 
- *A foreign key constraint is a condition that ensures the referential integrity of the data by enforcing a relationship between the foreign key and the referenced primary key*. 
	- This means that the constraint will guarantee that all data references are valid and consistent, preventing data from being added, updated, or deleted in a way that would break the relationships between tables.
	- It's worth noting that *foreign keys can exist without constraints, but constraints are helpful to maintain referential integrity*. 
	- Constraints also require additional computation to maintain, so at a certain scale, you may need to consider dropping some constraints if they become too costly in terms of performance.

```sql fold title:"Foreign key constraint example"
CREATE TABLE child (
  ID BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  parent_id BIGINT UNSIGNED,

  FOREIGN KEY (parent_id) REFERENCES parent(ID)
);

-- This creates a table `child` with two columns, `id` and `parent_id`. The `parent_id` column references the primary key of the `parent` table using a foreign key constraint. This constraint enforces the referential integrity of the data, ensuring that any data added to the `child` table is consistent with the data in the `parent` table.
```