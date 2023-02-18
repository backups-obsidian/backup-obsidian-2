---
created: 2022-04-17 15:43
updated: 2023-02-17 22:32
---
---
**Links**: [[102 AWS DVA Index]]
**Recommended Reads**: [[../101 AWS SAA/ElastiCache | ElastiCache]]

---
## Caching strategies
- If your data is changing very slowly then caching is a good idea.
- Data should be structured carefully for caching.
- Another important task is to find the appropriate caching strategy.
- You will be asked to look at pseudo code and find out the type of strategy.

### Lazy Loading / Cache-Aside / Lazy Population
- Lazy loading *loads data into cache only when necessary* i.e. when there is a cache miss.
- **Only requested data is cached** (the cache isn't filled up with unused data)
	- ![[attachments/Pasted image 20220514103230.png]]
- *Cost effective* as compared to write through since only requested data is cached.
- Cons
	- *Cache miss penalty that results in 2 round trips*, noticeable delay for that request. This means there is a **read penalty**.
	- **Stale data**: data can be updated in the database and outdated in the cache
 - **Writes are fast but reads are slow**. 
- Python pseudocode 
	- ![[attachments/Pasted image 20220514103411.png]]
	- `get_user` is for *read*.

> [!question]- You have an ElastiCache cluster with *small cache size*, so you want to ensure that only the data that's *requested* will be loaded into the cluster. Which caching strategy should you use?
> *Lazy Loading* would load data into the cache only when necessary (actively requested data from the database).

### Write Through
- You add or update the cache when database is updated. Because of this **data is never stale**.
	- ![[attachments/Pasted image 20220514104242.png]]
- There is a **write penalty** (each write requires 2 network calls) instead of a read penalty.
- **Writes are slow but reads are fast**.
- Cons
	- *Missing Data until it is added / updated in the DB*. 
	- So *nothing will be done if there is a cache miss*. 
	- Mitigation is to implement Lazy Loading strategy as well for cache miss scenario.
	- The **cache will become large and expensive** because the infrequently requested data is also written to the cache. 

- Python pseudocode
	- ![[attachments/Pasted image 20220514104053.png]]
	- `save_user` is for *write*

> [!tip] If you want *minimum latency read requests* and you are ok with write taking longer then go with *write through*.

> [!note] If you want your *cache to never go out of sync with the backend* then go with write through cache

## Cache Eviction and TTL
- Cache eviction is the process of removing items from cache. It can occur in three ways:
	- You delete the item *explicitly* in the cache
	- Item is evicted because the *memory is full* and it's not recently used (LRU)
	- You set an **item Time-To-Live** (or TTL)

- TTL can range from few seconds to hours or days

> [!tip] If too many evictions happen due to memory, you should scale up or out. Get a bigger cache.

## Tips 
- *Lazy Loading* / Cache aside is *easy to implement* and *works for many situations* as a foundation, especially on the read side.
- *Write-through is usually combined with Lazy Loading* as targeted for the queries or workloads that benefit from this optimisation.
- Setting a *TTL* is usually not a bad idea, *except when you're using Write through*. Set it to a *sensible value* for your application
	- **Use TTL** when the question mentions that the data should be **automatically deleted**.
- Only *cache the data that makes sense* (user profiles, blogs, etc...). Don't cache thinks like account balance.

> There are only two hard things in Computer Science: cache invalidation and naming things