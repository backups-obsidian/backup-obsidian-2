---
created: 2022-04-19 19:08
updated: 2022-05-14 09:41
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/ElastiCache DVA | ElastiCache DVA]]

---

## Introduction
- It is **managed Redis** or **memcached**.
- **In memory** database with **high performance** and **low latency** which provides **caching**.
- Helps **reduce load off of databases** for read intensive workloads. Common queries are going to be cached. 
-   Elasticache offers **sub millisecond latency**. [[DynamoDB]] offers sub *microsecond* latency.
-   *Not suitable for highly dynamic reads*. In that case it is better to get a read replica.
-   ElastiCache Redis has a **good use case for autocompletion**.
- Helps in making the *application stateless* without using ALB stick sessions.
- ElastiCache Redis is **HIPAA** eligible and **PCI DSS** compliant. ElatiCache *memcached* is *not HIPAA* compliant.
- Since Aurora and RDS have no in built caching Elastic Cache is a good use case. In case of *NOSQL DynamoDB, DAX is much better than ElastiCache*. In short ElastiCache Redis is **better suited for SQL databases**.

> [!caution] Using Elasticache requires *heavy application code changes*.

- While using caching the cache must have an *invalidation strategy* to make sure only the most current data is used in there.

> [!question]- How is memcached different from redis?
> - Memcached is **highly distributed and multi threaded** and you use it in applications where you can *afford to lose data*.
> - *Redis is multi AZ and highly available* whereas memcached is not.
> - *Redis has backup and restore features* whereas memcached doesn't

> [!caution] All caches in elasticache DONT support IAM authentication.

> [!question] Redis use case: **Redis Sorted Sets**.

## Security
- We **have SGs** in ElasticCache also.
### Redis Auth
- Using Redis AUTH command can *improve data security* by **requiring the user to enter a password/token** before they are granted permission to execute Redis commands on a password-protected Redis server. 
- Support SSL in flight encryption.
- **Parameters** that can be provided *while creating a redis cluster*: 
	- `--auth-token`: for *redis auth*
	- `--transit-encryption-enabled`: for transit encryption.
	- `AtRestEncryptionEnabled`: encrypts in memory data.

### Memcached 
- It supports *SASL based authentication*.

## Patterns in Elastic Cache
- **Lazy loading**: You write to the cache only when there is a *cache miss*.
- **Write through**: Write to cache *when written to DB* - no stale data.
- **Session Store**: Refresh cache on expiry of TTL.

For more info on caching patterns refer [[../102 AWS DVA/ElastiCache DVA#Caching strategies | Caching Strategies]]

