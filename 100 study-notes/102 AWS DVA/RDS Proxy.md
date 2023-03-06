---
created: 2023-03-01 08:38
updated: 2023-03-05 20:05
---
---
**Links**: [[102 AWS DVA Index]]

---
## RDS Proxy
- **Fully managed** proxy for RDS.
- *Serverless*, *autoscaling* and *highly available*.
- RDS proxy allows apps to **pool and share DB connections** established with the database.
	- Instead of each application connecting to the database we connect to the RDS proxy which pools the database connections.

> [!note]- This will help in improving  database efficiency by **reducing the stress on database resources** (e.g., CPU, RAM) and **minimise open connections** (and timeouts).

- Since RDS proxy is highly available it can *reduce the RDS and Aurora failover time by 66%*. 
- Supports RDS (MySQL, PostgreSQL, MariaDB) and Aurora (MySQL, PostgreSQL)
- **NO code changes required**.
- RDS proxy can be used to **enforce IAM Authentication for DB**, and securely store credentials in AWS Secrets Manager.
- RDS proxy can be deployed in a private subnet or a public subnet.
- Imagine you are using lambda to connect to the RDS database. 
	- The lambdas can scale very quickly thereby rapidly increasing the number of database connections. This can lead to *TooManyConnections* exception.
	- This can lead to a number of open connections and timeouts.
	- This can be solved making lambda functions connect to the RDS Proxy.
	- ![[attachments/Pasted image 20230301084521.png]] 