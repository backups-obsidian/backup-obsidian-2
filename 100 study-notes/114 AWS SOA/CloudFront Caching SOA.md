---
created: 2023-03-05 18:26
updated: 2023-03-05 18:53
---
---
**Links**: [[114 AWS SOA Index]]
**Recommended Reads**: [[../102 AWS DVA/CloudFront DVA#Caching | CloudFront Caching]]

---
## Caching
- Cache based on
	- **Headers**
	- **Session Cookies**
	- **Query String Parameters**
- The cache *lives at each CloudFront Edge Location*
- You want to maximise the cache hit rate to minimise requests on the origin.
- *Control the TTL* (*0 seconds to 1 year*), can be set by the origin using the `Cache- Control: max-age` header.
- You can *invalidate part of the cache* using the `CreateInvalidation` API

> [!note] **All** means the **WORST caching behaviour**, **None** means the **BEST caching behaviour**.

### Cache Behaviour for Headers
- **Forward all headers** to your origin
	- **NO caching**, *every request is sent origin*
	- *TTL must be set to 0*
- **Forward a whitelist of headers**
	- *Caching based on values in all the specified headers*
- **Forward only the default headers** (*NONE*)
	- No caching based on request headers
	- *Best caching performance*

- *Only the whitelisted headers will be passed to the origin*.
	- ![[attachments/Pasted image 20230305183047.png]]

> [!note] **Less headers means better caching**.

#### Origin Header vs Cache Behaviour
- *Origin Custom Headers*:
	- Origin-level setting
	- **Add a constant header / header value** for all requests to origin

- *Behaviour setting*:
	- Cache-related settings
	- **Contains the whitelist of headers to forward**

### Caching TTL
- `Cache-Control: max-age` for setting the TTL.
- If the *origin always sends back the header Cache-Control* then you can *set the TTL to be controlled only by that header*.
- In case you want to *set min/max boundaries*, you choose "customise" for the Object Caching setting.
	- ![[attachments/Pasted image 20230305184227.png]]
- In case the **Cache-Control header is missing**, it will default to *default value*.

### Cache Behaviour for Cookies
- **Forward all cookies**
	- *Worst* caching performance.
- **Forward a whitelist** of cookies
	- Caching based on values in all the specified cookies.
- **Default (NONE)**: do not process the cookies 
	- Caching is not based on cookies.
	- Cookies are not forwarded.
	- Best caching performance.

> [!note]- **Less cookie values means better caching**.
> ![[attachments/Pasted image 20230305184744.png]]

### Cache Behaviour for Query Strings
- **Forward all query strings**
	- Caching based on all parameters
	- Worst caching performance
- **Forward a whitelist** of query strings
	- Caching based on the parameter whitelist
- **Default (NONE)**: do not process the query strings
	- Caching is not based on query strings
	- Parameters are not forwarded
	- *Best caching performance*.

## Maximising cache hits
- **Separate static and dynamic distributions**.
	- ![[attachments/Pasted image 20230305185101.png]]
- *Monitor* the CloudWatch metric `CacheHitRate`
- *Specify* how long to cache your objects: `Cache-Control: max-age` header
- **Specify NONE or the minimally** required headers, cookies and query string parameters.