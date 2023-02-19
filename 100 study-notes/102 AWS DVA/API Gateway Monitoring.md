---
created: 2022-05-26 20:09
updated: 2023-02-18 08:37
---
---
**Links**: [[102 AWS DVA Index]]

---
## Monitoring, Logging & Tracing
### CloudWatch Logs
- Enable CloudWatch *logging at the Stage level* (with *Log Level*)
- Can override settings on a per API basis (ex: ERROR, DEBUG, INFO)
- Log contains information about *request/response body*

### X-Ray
- *Enable tracing* to get extra information about requests in API Gateway
- *X-Ray API Gateway + AWS Lambda* gives you the full picture 

### CloudWatch Metrics
- Metrics are by stage, Possibility to enable detailed metrics
- There are two types of *API logging* in CloudWatch: **execution logging** and **access logging**.
- **CacheHitCount** & **CacheMissCount**: *efficiency of the cache*
	- *CacheMissCount* tracks the number of *requests served from the backend* in a given period, when *API caching is enabled*. 
	- *CacheHitCount* track the number of *requests served from the API cache* in a given period.
- **Count**: The *total number API requests* in a given period.
- **IntegrationLatency**: The time between when API Gateway *relays a request to the backend and when it receives a response from the backend*.
- **Latency**: The time between when API Gateway receives a request from a client and when it returns a response to the client. 
	- The latency includes the *integration latency and other API Gateway overhead*.
	- `Latency > IntegrationLatency`

> [!note]- How to differentiate between integration latency and latency?
> - **Client** present means *latency*. 
> - **Relays** present means integration *latency*.

- 4XXError (client-side) & 5XXError (server-side)
	
> [!caution] The maximum amount of time an API Gateway can perform any request is **29s** so if *Latency or IntegrationLatency is > 29s* then we will see a **timeout** from API Gateway

### API Throttling
- Account Limit
	- API Gateway throttles requests at **10000 rps across all API**. This means that if one of the APIs is under heavy use then other APIs can also be throttled.
	- *Soft limit* that can be increased upon request
- In case of throttling => **429 Too Many Requests** (retriable error). Client side error.
- Can set **Stage limit** & **Method limits** to improve performance
- Or you can define **Usage Plans** to throttle per customer

> [!important] Just like Lambda Concurrency, if one API that is overloaded, if not limited, can cause the other APIs to be throttled

### Errors
- *4XX* means *Client errors*
	- **400**: *Bad Request*
	- **403**: *Access Denied*, WAF filtered
	- 429: Quota exceeded, Throttle
- *5XX* means *Server errors*
	- **502**: Bad Gateway Exception, usually for an **incompatible output returned from a Lambda proxy integration** backend and occasionally for out-of-order invocations due to heavy loads.
		- Like returning XML instead of JSON when using **lambda proxy**.
	- **503**: *Service Unavailable Exception*. Backend is unavailable.
	- **504**: *Integration Failure*. 
		- Example: Endpoint Request Timed-out Exception 
		- API Gateway requests **time out after 29s maximum**

