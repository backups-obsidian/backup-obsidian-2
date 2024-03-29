---
created: 2022-05-16 12:39
updated: 2023-02-14 19:36
---
---
**Links**: [[102 AWS DVA Index]]

---
## Caching
- Caching reduces the number of calls made to the backend
- **Default TTL** is **300s** (min: 0s, max: **3600s**)
	- TTL of **0** means **NO caching (caching is disabled)**.
- **Caches are defined per stage**
- Possible to *override cache settings per method*
- Cache *encryption option*
- Cache capacity between 0.5GB to 237GB
- Cache is **expensive** 
	- *Makes sense in production, may not make sense in dev / test*
	- Enable cache for dev/test only when required.

### Cache Invalidation
- Able to flush the entire cache (invalidate it) immediately using the console.
- Clients can invalidate the cache with header: `Cache Control: max-age=0` (**with proper lAM authorisation**)
	- ![[attachments/Pasted image 20220526162214.png]]
- If you don't impose an *InvalidateCache policy* (or choose the Require authorisation check box in the console), any client can invalidate the API cache.
	- This can be a big problem
