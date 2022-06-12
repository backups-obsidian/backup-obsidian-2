---
created: 2022-04-19 16:22
updated: 2022-05-01 12:06
---
---
**Links**: [[101 AWS SAA Index]]

---
## DMS
- It stands for **Database Migration Service**.
- Quickly and securely **migrate on-premise databases to AWS**, resilient, self healing
- The *source database remains available during the migration*. **No downtime**.
- Supports:
    - **Homogeneous migrations**: ex Oracle to Oracle
    - **Heterogeneous migrations**: ex Microsoft SQL Server to Aurora
- Continuous Data Replication using **CDC (Change Data Capture)**
- You must create an *EC2 instance* to perform the *migration tasks*.
- DMS can be used to **replicate the data from the databases** into Amazon Redshift.

> [!important]+ It can act as a bridge from *S3 to KDS*.
> - Not only for migrating databases but *also for migrating data between different sources*.
> - It can also be used for **data replication** related to databases.

> [!note] Keywords: *replication*, *database migration* 

## SCT
- It stands **Schema Conversion Tool**.
- If the **source** and **target database don’t have the same engine** then we have to use SCT for conversion before DMS.
- RDS is just the platform and not the engine. So for migrating from on premise MySQL server to RDS you don't need SCT.
- We can use **Schema Conversion Tool (SCT) to extract and load the data** to an AWS Snowball Edge device and then use the AWS Database Migration Service (DMS) to migrate the data to Amazon DynamoDB.

## AWS Server Migration Service (SMS)
- **Incremental** replication of on-premise **live** servers to AWS.
- On premise to AWS.