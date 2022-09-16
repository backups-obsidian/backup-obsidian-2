---
created: 2022-09-15 21:09
updated: 2022-09-16 16:12
---
---
**Links**: [[111 KodeCloud Index]]

---
## Databases
- In SQL all rows together form a table, multiple tables form a database and multiple database forms a server.
- In NoSQL *individual documents together form a collection*, *multiple collections form a database* and multiple databases form a server.

### MySQL
- Default data directory of MySQL is `/var/lib/mysql`
- Default log location is `/var/log/mysqld.log`
- When creating a user in MySQL in addition to username & password we also specify the location from which the user is allowed to connect to the Database.
	- ![[attachments/Pasted image 20220916155631.png]]
	- ![[attachments/Pasted image 20220916155721.png]]
- If we want to user to connect from any system then we specify it using the `%` system
	- ![[attachments/Pasted image 20220916155755.png]]
- To restrict the actions of the user on different tables in a database we use *grants*.
	- ![[attachments/Pasted image 20220916160015.png]]

### MongoDB
- We can find mongodb logs at `/var/log/mongodb/mongodb.log`
- Initially mongodb only listens on `127.0.0.1` so we cannot access it from outside. 
	- We have to change this in the conf file
- We can change information like location where data is stored, log location, port etc in the `/etc/mongod.conf`
	- ![[attachments/Pasted image 20220916161038.png]]
- Mongo shell usage
	- ![[attachments/Pasted image 20220916161157.png]]
