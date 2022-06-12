---
created: 2022-04-19 16:22
updated: 2022-05-04 10:32
---
---
**Links**: [[101 AWS SAA Index]]

---
## Database types
-   **RDBMS** 
	-  SQL / **OLTP**(online transaction processing)  
	- `RDS`, `Aurora` 
	- **great for joins**

-   **NoSQL database** 
	- `DynamoDB` (~]SON), `ElastiCache` (key / value pairs) 
	- no joins, no SQL.

-   **Object Store** 
	-  `S3` (for **big objects**) / `Glacier` (for **backups** / archives). 
	- S3 and glacier don’t look like database but they are databases which are used to store large objects.
	- It is a key value store for large objects

-   **Data Warehouse**  
	- (= SQL **Analytics** / BI) 
	- `Redshift` (OLAP), `Athena`

-   **Search**  
	- `ElasticSearch` (]SON) 
	- **free text, unstructured searches**

-   **Graphs** 
	-  `Neptune` displays relationships between data
	- **Neptune is fully managed**.
	- social media apps

### Redshift
- Based on *postgres*.
- It can run *complex analytical queries*.
- Used of **warehousing**, **OLAP** (online analytical processing), **BI/analytics**.
- *Enterprise-level*, *petabyte scale*, **fully managed** data warehousing service.
- **Columnar** storage of data.
- Massive **parallel** query execution.
- We have *leader (query planning)* and *compute nodes (performing queries)*.
- *Data* can be *loaded* into Redshift using **Kinesis data firehose**, **S3** via *copy* command and EC2 instances.
- It is faster than Athena. But it is *not serverless like Athena*. Also it is costlier than Athena.

#### Disaster Recovery
- There is **no multi AZ mode** in redshift. 
- For *Disaster Recovery* (DR) we have to use snapshots.
- You can take **snapshots** manually and restore new cluster from snapshots.
- All the snapshots are *stored* in **S3**.
- You can configure Amazon Redshift to *automatically copy snapshots* (automated or manual) of a cluster *to another AWS Region*. 
	- For this to work *automated snapshots must be enabled*.
	- Enable **Cross-Region Snapshots Copy in your Amazon Redshift Cluster**.


#### Redshift Spectrum
- Perform queries directly against **S3**
- **No need to load** data.
- Keywords for questions are **Least** amount of **effort**, **minimum cost**.

#### Redshift enhanced VPC routing
- Redshift forces **all COPY and UNLOAD traffic** moving between your cluster and data repositories *through your VPCs*

### Glue
- It is an **ETL (Extract, Transform and Load) service** in AWS. 
- It is used to **prepare** and **transform data for analytics**.
- Fully **serverless**.
- Glue data catalog
- *Takes* data from databases like *RDS*, *DynamoDB*, *S3* and then after ETL jobs *sends* it to BI tools like *Redshift*, *Athena*, *EMR*. 

### ElasticSearch
-   In DynamoDB you can only find by the primary keys or indexes.
-   With ElasticSearch we **can search any field** and even **partial matches**.
-   It is used to **complement other databases**.
-   Comes with **Kibana** (visualisation) & **Logstash** (log ingestion) - **ELK** stack