---
created: 2023-03-05 17:42
updated: 2023-03-05 18:54
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

## CloudFront with ALB sticky sessions
- Must **forward / whitelist the cookie that controls the session affinity** to the origin to allow the session affinity to work.
	- ![[attachments/Pasted image 20230305181514.png]]
