---
created: 2022-04-19 16:22
updated: 2023-02-19 09:01
---
---
**Links**: [[101 AWS SAA Index]]

---
## Encryption

- **At rest encryption** is done using *AWS KMS*.
-  Possibility to encrypt the master and read replicas with AWS KMS - AES 256.
- **In flight encryption** is done by *SSL certificates* to encrypt the data coming to RDS.
	- Amazon RDS *creates and installs a SSL certificate* on the DB instance when Amazon RDS provisions the instance. 
	- You can *download the root certificate* from AWS and *use it* when connecting to the database.
	- You cannot use your own self signed certificates.

- **TDE** (Transparent Data Encryption) is possible for **Oracle** and **SQL Server**. This is **for data at rest**. 
- We can *force SSL* connections to out DB instance by 
	- Setting `rds.force_ssl`(**parameter groups**) to **true** and then rebooting the DB instance *or*
	- DB can be configured to allow only SSL connections.

- Snapshots of unencrypted database are unencrypted. Snapshots of encrypted database are encrypted.
- *We can copy an unencrypted snapshot to an encrypted one*. This is useful when you want to encrypt your database.

> [!question]- How to encrypt and unencrypted database?
> - Create a snapshot of the un-encrypted database
> - Copy the snapshot and enable encryption for the snapshot
> - Restore the database from the encrypted snapshot
> - Migrate applications to the new database, and delete the old database

> [!caution]+ If master is NOT encrypted then NOT possible to encrypt the read replicas.
> So if you want to create encrypted read replicas from an unencrypted master database then 
> - Create an encrypted snapshot of the unencrypted master DB
> - **Create a new encrypted master DB from the snapshot**
> - Create an encrypted read replica.

## Network Security
- Deploy RDS instances in a private subnet.
- RDS also **uses security groups** and it works in the same way as that of the EC2 instances.
- *IAM policies* help us control *who can manage the database*.

## IAM Authentication
- Generally for *logging into* the database *we need a username and password*.
- We can use IAM authentication for additional security. **No need of password**.
- **Authentication tokens** are used which are valid for **15 mins**. Short lived authentication tokens.
- Authentication token is obtained from IAM and RDS API calls.
- The major benefit of using IAM authentication is that we can **centrally manage users** using IAM *instead of using the DB*.
	- Can leverage *IAM Roles* and EC2 Instance profiles *for easy integration*.
- It is supported by **Postgres** and **MySQL**.
- IAM authentication to RDS is supported, which must be *achieved by attaching an IAM role* the AWS Lambda function