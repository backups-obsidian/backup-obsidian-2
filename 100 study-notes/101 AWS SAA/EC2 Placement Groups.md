---
created: 2022-04-19 16:22
updated: 2022-05-05 16:08
---
---
**Links**: [[101 AWS SAA Index]]

---
-   It helps us **control how our EC2 instances are going to be placed within the AWS infrastructure.**
-   We don’t get access to the hardware but we let AWS know how we want our EC2 machines to be placed.
-   There is **no charge** for creating a placement group

## Cluster
- Low latency and high throughput. Like **HPC**.
- Placed in the **same rack in the same AZ**.
- All instances are on the same hardware.

## Spread
- Minimise failure risk.
- Limited **7 instances per AZ per placement group**. So if you want *15 instances* in spread placement group then you will need *3 AZs*. **Major CON**.
- Instances are on different hardware in different AZ.
- **Critical applications**.

## Partition
- We have **instances spread across partitions in multiple AZs**.
- We can have upto **7 partitions per AZ**. Each partition can have many EC2 instances.
- Each *partition represents a hardware rack*.
-   By having many partitions you are making sure that your EC2 instances are distributed across many hardware racks. **Each partition is isolated from failure since they are on a different rack**
-   We can have **100s of EC2 instances** with this setup.
-   We can get **partition information using the EC2 metadata service.**
-   Use cases would be **big data applications like Cassandra**, **HBase**, **Kafka**

> [!tip] If you receive a *capacity error* when launching an instance in a placement group that already has running instances, *stop and start all of the instances in the placement group*, and try the launch again. 

> [!note] If you have an application that needs a large number of instances and minimise hardware failure then go for partition over spread.