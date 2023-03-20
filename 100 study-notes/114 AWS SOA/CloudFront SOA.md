---
created: 2023-03-05 17:42
updated: 2023-03-20 08:49
---
---
**Links**: [[114 AWS SOA Index]]

---
## Access Logs
- *Logs every request* made to CloudFront into a logging **S3 bucket**.
	- ![[attachments/Pasted image 20230305174405.png]]
	- We can separate the logs in a single bucket by adding a prefix.
- The logging bucket should be different from the origin bucket.

> [!note] We can *analyse CloudFront Access Logs with Athena* for discovering traffic patterns.

## Reports
- It's possible to generate reports on:
	- *Cache Statistics* Report
	- Popular Objects Report
	- Top Referrers Report
	- Usage Reports
	- Viewers Report
- These *reports are based on the data from the Access Logs*.
- Similar to google analytics.

## Troubleshooting
- CloudFront **caches HTTP 4xx and 5xx status codes** returned by S3 ( or the origin server)
- *4xx* error code indicates 
	- That user *doesn't have access* to the underlying bucket *(403)*
	- The object user is requesting is *not found (404)*
- *5xx* error codes indicates *Gateway issues*
- Your CloudFront distribution might send error responses with *HTTP status code 400 Bad Request*, and a message similar to the following: `The authorization header is malformed; the region '<AWS Region>' is wrong; expecting '<AWS Region>'`.
	- This problem can occur when your *CloudFront distribution's origin is an Amazon S3 bucket* and you have *moved the S3 bucket from one AWS Region to another*. 
	- To fix this error, *update your CloudFront distribution so that it finds the S3 bucket in the bucket's current AWS Region*.

> [!question]- Users of a web application that is served using Amazon CloudFront have complained about receiving 4XX and 5XX errors. *A SysOps Administrator wants to monitor for elevated error rates in Amazon CloudFront*. Which metric should be monitored?
> **TotalErrorRate**.

## CloudFront with ALB sticky sessions
- Must **forward / whitelist the cookie that controls the session affinity** to the origin to allow the session affinity to work.
	- ![[attachments/Pasted image 20230305181514.png]]
