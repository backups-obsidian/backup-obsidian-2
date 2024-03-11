---
created: 2024-03-09 11:10
updated: 2024-03-09 13:16
---
---
**Links**: [[121 MySQL for Developers Index]]

---
- Some rules for defining the schema:
	- **Use simple column types**. For example if the column will contain numbers then use integer datatype and not string.
	- **Should reflect reality**. *So if the data is not nullable then the column should also be not nullable*.
	- **Keep the column type small**. For example if you foresee that the column will only contain integers from 1 - 1000 then don't use bigint as the column datatype.

## Different column data types
### Integers
- There are 5 different types in MySQL: `TINYINT`, `SMALLINT`, `MEDIUMINT`, `INT`, `BIGINT`.
	- All other integer data types are **aliases** to the above datatypes. For example `INTEGER` is just an alias for `INT` datatype. It is not another datatype.

![[attachments/Pasted image 20240309111522.png]]

- If we have a books table and we have `num_pages` as one of the columns, we can declare its datatype as: `num_pages SMALLINT UNSIGNED`.
	- We use `UNSIGNED` as we know there won't be any negative numbers, and 65535 is a big range for the number of pages.

> [!note] `BOOLEAN` datatype is nothing but a `TINYINT` column.

### Decimal
- There are 3 different decimal types in MySQL: `DECIMAL`, `FLOAT`, `DOUBLE`.
	- *`NUMERIC` is just an alias for `DECIMAL`*.
- **`DECIMAL` is a fixed point (exact value) datatype** whereas **`FLOAT` and `DOUBLE` are floating point (approximate value) datatype**.
	- So use *`DECIMAL` to store currency*.
	
> [!note] If you add 0.7 and 0.6 DECIMALs then the result will be 1.3 but if you add 0.7 and 0.6 Floats, result won't be 1.3.

- We often define decimal datatype using `decimal(10,2)`
	- This means that there are 2 digits after the decimal point (fraction) and 8 digits in front of it.
	- Its very important to define it carefully since this also defines the range of numbers that can be stored.
		- `decimal(10,2)` can store more numbers than `decimal(10,4)`.
- *`FLOAT` is 4 bytes and `DOUBLE` is 8 bytes*.
	- So if we need *single precision then we use `FLOAT`*, if we need *double precision then we use `DOUBLE`*.

### String
- Different string types in MySQL are: `CHAR`, `VARCHAR`, `BINARY`, `VARBINARY`, `BLOB`, `TEXT`, `ENUM`, `SET`.
- `VARCHAR` is used for *variable length string* and `CHAR` is used for *fixed length string*.
	- An example of using of a column using `CHAR` datatype would be one storing md5 hashes.

> [!question]- What's the difference between `char(100)` and `varchar(100)`?
> - When we use `char(100)` we use the space of 100 characters even when we store less than 100 characters in it. The left out space is filled with empty space. **So this will always take 100 Bytes**.
> - When we use `varchar(100)` we only use the space of characters we store in it. **This will at max take 100 Bytes**.

- Character set: what characters are allowed in the column.
- Collation set: how are characters compared. For example is a > A or a === A or e === è.
- `BINARY` and `VARBINARY` is used for storing small amounts of binary data.
	- It is rarely used but can be used to store the binary version of a md5 hash.
		- Binary version of a md5 hash occupies less space on disk when compared to storing it as varchar.
- `TEXT` column can be used to store *huge amounts of text*.
	- We have `TINYTEXT`, `TEXT`, `MEDIUMTEXT` and `LONGTEXT`.
- `BLOB` column can be used to store *huge amounts of binary data*.
	- We have `TINYBLOB`, `BLOB`, `MEDIUMBLOB` and `LONGBLOB`.
	- We can technically use `BLOB` type to store files, images etc but it is not a good practice.
- **Grouping of different string types**:
	- Store *character data*: `CHAR`, `VARCHAR`, `TEXT`.
	- Store *binary data*: `BINARY`, `VARBINARY`, `BLOB`.
- `ENUM` looks like a string but it is stored like an integer.
	- It is particularly useful when we have a specific list of allowable values that can go into a column.
	- *It stores the underlying integers starting from 1 in the table and displays it as a string*.
	- When we order by an `ENUM` column, it orders by the integers and the not the string values.
	- *In case we want to add new values to the `ENUM` column, we will have to change the schema*.

### Time
- Different options to store temporal data in MySQL: `DATE`, `DATETIME`, `TIMESTAMP`, `YEAR`, `TIME`.

![[attachments/Pasted image 20240309122257.png]]

- If we just need to store the date the we should use the `DATE` type column.
- If we need to store timestamp with date then we can use `DATETIME` or `TIMESTAMP`.
	- Note that `TIMESTAMP` can only hold data till 2038.
- *`TIMESTAMP` tries to assist us by converting time depending on the timezone*.
- Creating `created_at` and `updated_at` columns using `timestamp`

```sql fold 
create table users (
	id serial,
	created_at timestamp default CURRENT_TIMESTAMP,
	updated_at timestamp default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP
); 
```

### JSON
- `JSON` column is different from a normal string column as it validates the JSON.
- `JSON` column is a big column and is stored in a special binary format.
	- Only select the `JSON` column if its needed. That means don't do a `select *` on a table that has a `JSON` column.

### Generated Columns
![[attachments/Pasted image 20240309130004.png | An example of using generated columns to extract the domain from the email address]]

- *If the generated column is declared as `STORED` then it will be stored on the disk. If is declared as `VIRTUAL` then it will be calculated everytime at runtime*.
	- If the value is easy to calculate then use `VIRTUAL`, if its difficult to calculate then use `STORED`.
	- `VIRTUAL` is the default.