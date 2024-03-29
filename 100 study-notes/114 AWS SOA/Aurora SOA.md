---
created: 2023-03-05 22:42
updated: 2023-03-20 09:07
---
---
**Links**: [[114 AWS SOA Index]]

---
## [[../101 AWS SAA/Aurora#Introduction | Introduction]]
## [[../101 AWS SAA/Aurora#High Availability and Read Scaling | HA and Read Scaling]]

## Backup, Backtracking and Restores
- **Automatic Backups**
	- Retention period 1-35 days (**CANT be disabled**)
	- PITR, restore your DB cluster within 5 minutes of the current time
	- Restore to a *new DB cluster*
- Aurora **Backtracking**
	- *Rewind the DB cluster back and forth in time* (up to 72 hours)
	- **DOESN'T create a new DB cluster** (*in-place* restore)
	- Supports Aurora *MySQL only*
- Aurora **Database Cloning**
	- Creates a *new DB cluster that uses the same DB cluster volume* as the original cluster.
	- Uses *copy-on-write* protocol (use the original/single copy of the data and allocate storage only when changes made to the data)
	- Example: *create a test environment using your production data*

> [!note] You can migrate an RDS MySQL snapshot to Aurora MySQL Cluster.

> [!question]- You have an Aurora DB Cluster where the automatic backup is enabled with a retention period of 10 days. You're using this Aurora DB Cluster for testing purposes, so you want to *disable automatic backups to reduce costs*. What should you do?
> You *can't disable Aurora DB Cluster Automatic Backups*.

> [!tip] If the question asks for a particular Aurora engine type, always go for **MySQL**.

## CloudWatch Metrics
- **`AuroraReplicaLag`**: amount of lag when replicating updates from the primary instance.
	- `AuroraReplicaLagMaximum`: max. amount of lag across all DB instances in the cluster
	- `AuroraReplicaLagMinimum`: min. amount of lag across all DB instances in the cluster

> [!note]- If *replica lag is high*, that means the *users will have a different experience based on which replica* they get the data from (**eventual consistency**)
> Like for example if a user adds an item in a cart and then refreshes the page then he may not see the new item added to the cart.

- `DatabaseConnections`: current number of connections to a DB instance
- `InsertLatency`: average duration of insert operations

## Miscellaneous
> [!question]- Consider this scenario - the *primary instance* of an Amazon Aurora cluster is unavailable because of an *outage* that has affected an entire AZ. *The primary instance and all the reader instances are in the same AZ*. As a SysOps Administrator, what action will you take to get the database online?
> You must *manually create one or more new DB instances in another AZ*.