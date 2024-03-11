---
created: 2024-03-11 12:15
updated: 2024-03-11 13:48
---
---
**Links**: [[../100 home|100 home]]

---
## Redis
- **Redis means remote dictionary server**.
- Redis is an **in memory** **key value** store.
- *Redis is incredibly fast because it stores its data in memory and not in disk*.
- *Key is a string but value can be anything*.
- It is single threaded.
	- Multithreaded when we have durability enabled.
- *Different options for durability*:
	- **Journaling** using AOF (append only file) - *default*.
		- Redis will write an entry to a log file for every write operation received.
		- When restarting Redis it will replay the AOF file to rebuild the state.
	- *Snapshotting (RDB)*
- It uses TCP for communication.
- Message format is RESP (Redis Serialization Protocol).
- Redis supports Pub / Sub model (similar SNS).
- Keys can have an expiration time.
- **Main redis data types**:
	- ![[attachments/Pasted image 20240311133212.png]]

```bash fold title:"Using redis and the redis cli"
sudo docker run -d --name redis redis

# connecting to the redis cli
sudo docker exec -it redis redis-cli
```

### Simple Commands
- Get all the keys: `keys *`
- Setting a key: `set key-name value`
- Get a key: `get key-name`
- Delete a key: `del key-name`