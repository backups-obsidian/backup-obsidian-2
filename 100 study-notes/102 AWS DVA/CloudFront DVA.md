---
created: 2022-05-15 08:57
updated: 2023-03-01 17:52
---
---
**Links**: [[102 AWS DVA Index]]
**Recommended Reads**: [[../101 AWS SAA/CloudFront | CloudFront]]

---
## Caching
- The cache lives at each CloudFront Edge Location so there are as many caches as there are CloudFront edge locations.
- You want to *maximise the cache hit rate to minimise requests on the origin*. In case of cache miss the request is forwarded to the origin.
	- ![[attachments/Pasted image 20220515090054.png]]

- We can invalidate a part of the cache using the `CreateInvalidation` API
- In CloudFront it is common to separate caching of dynamic content from static content. 
	- For caching of static content we don't use any caching rules to maximise the cache hit. We use these rules for dynamic content.
	- ![[attachments/Pasted image 20220515090513.png]]

- By *default* the S3 content is cached for *1 day*. 

### Cache Key
- CloudFront *identifies each object* in the cache using the **Cache Key**.
	- It is a unique identifier for every object in the cache.
- By default, consists of **hostname + resource portion of the URL**.
	- ![[attachments/Pasted image 20230301090413.png]]

### Cache Policy
- *Cache* based on:
	- **Headers**: None, Whitelist
	- **Session Cookies**: None, Whitelist, Include All except, All
	- **Query String Parameters**: None, Whitelist, Include All except, All

- You *can't cache based on HTTP Methods*.
- We use *TTL to control the cache hit*. TTL (0 seconds to year), can be set by the origin using the Cache *Control header*, *Expires header*.
- We can define the cache policy while creating the distribution or after creating it (behaviours section). 
	- We can also make our *own caching policy* if we don't like the ones *predefined by AWS*.

> [!note]- **All** *HTTP headers, cookies, and query strings* that you include in the *Cache Key* are automatically included in origin requests.
> ![[attachments/Pasted image 20230301091018.png]]

- Using the **origin request policy** we can specify values that you want to *include in origin requests without including them in the Cache Key* (no duplicated cached content)

- Cache policy vs Origin request policy
	- ![[attachments/Pasted image 20230301091307.png]]
	- We cache based on the cache policy. 
	- The origin request policy enhances the request to the origin. 
	- Caching will not happen based on origin request policy. 

### Different ways of cache invalidation
- In case you update the back-end origin, *CloudFront doesn't know about it and will only get the refreshed content after the TTL has expired*.
	- This is an undesirable behaviour since we would want our cache to get refreshed as soon as our backend gets updated.
- **Invalidate the file** from edge caches. 
	- If you want to **invalidate the cache then go to the invalidations tab and put a wildcard (`*`)**.
	- We can do a *partial cache* invalidation using **`/images/*`**
	- ![[attachments/Pasted image 20230301091945.png]] 
- **Use file versioning** to serve a different version of the file that has a different name.
	- When you update existing files in a CloudFront distribution, *AWS recommends that you include some sort of version identifier* either in your *file names* or in your *directory names* to give yourself better control over your content. 
		- For example, instead of naming a graphic *file* image.jpg, you might call it image_1.jpg. When you want to start serving a new version of the file, you'd name the new file image_2.jpg, and you'd update the links in your web application or website to point to image_2.jpg. 
		- Alternatively, you might put all graphics in an images_v1 *directory* and, when you want to start serving new versions of one or more graphics, you'd create a new images_v2 directory, and you'd update your links to point to that directory. 
	- **With versioning, you don't have to wait for an object to expire before CloudFront begins to serve a new version of it, and you don't have to pay for object invalidation**. 

> [!note]- *Invalidating cache* is an **expensive operation** as compared to using *file versioning*.
> If the question asks for cache invalidation then *prefer file versioning instead of cache invalidation*.

> [!question]- A website is being delivered using Amazon CloudFront and a Developer recently modified some images that are displayed on website pages. Upon testing the changes, the Developer noticed that the new versions of the images are not displaying. What should the Developer do to *force the new images to be displayed*?
> *Invalidate the old versions* of the images on the edge caches.

---
- Deploying your website using CLI: NOT needed for exam.
	- `aws s3 cp public/ s3://bucket-name/`
	- Make sure that while creating the distribution you have specified `index.html` to be used when root path is hit.
	- `aws s3 rm s3://bucket-name --recursive`

## Security
-  [[../101 AWS SAA/CloudFront#CloudFront Georestriction | CloudFront Georestriction]]
- HTTPS

### HTTPS
- With *viewer* and *origin protocol policy* we are able to **how HTTPS is enforced** between client and the edge location and the edge location and origin.
	- ![[attachments/Pasted image 20220515104445.png]]
- **Viewer Protocol Policy**:
	- *Redirect HTTP to HTTPS*
	- Or *use HTTPS only*
- **Origin Protocol Policy**: (HTTP or S3):
	- *HTTPS only*
	- Or *Match Viewer*: (HTTP => HTTP & HTTPS => HTTPS)

> [!note] [[../101 AWS SAA/S3 Websites#Websites| S3 Websites]] don't support HTTPS but if you put your S3 website behind a CloudFront distribution then it will be secured by HTTPS.

> [!question]- A company runs a popular website behind an Amazon CloudFront distribution that uses an Application Load Balancer as the origin. The Developer wants to set up custom HTTP responses to 404 errors for content that has been removed from the origin that redirects the users to another page. The Developer wants to use an AWS Lambda@Edge function that is associated with the current CloudFront distribution to accomplish this goal. The solution must use a minimum amount of resources. Which CloudFront event type should the Developer use to invoke the Lambda@Edge function that contains the redirect logic?
> Origin Response.

## CloudFront Signed URL Process
- More about [[../101 AWS SAA/CloudFront Advanced#CloudFront Signed URL Cookies | CloudFront Signed URLs]]
- *Two types of signers*:
	- **Trusted key groups** (*recommended*)
		- Can leverage APIs to create and rotate keys (and IAM for API security)
	- An AWS Account that contains a *CloudFront Key Pair*
		- Need to *manage keys using the root account* and the AWS console
		- **Not recommended** since **only the root user can create CloudFront key pairs**.
		- When you use the *root user* to manage CloudFront key pairs, you can **only** have up to **two active CloudFront key pairs** per AWS account.
- You **generate your own public / private key** 
	- The *private key* is used by your *applications* (e.g. EC2) to *sign URLs*
	- The *public key* (uploaded) is used by *CloudFront to verify URLs* 
- After creating the keys we add the public keys in our CloudFront distribution and then add the public keys to key groups.
- In our CloudFront distribution, we *can create one or more trusted key groups*.

## Real time logs
- CloudFront provides **real-time logs** that contain *detailed information* about viewer requests and responses served by CloudFront, such as the viewer's IP address, the time and date of the request, the URI of the requested object, the HTTP status code returned to the viewer, and more.
	- You can use these real-time logs to *gain insights* into your application's usage patterns and *troubleshoot issues* with your CloudFront distribution.
- CloudFront real-time logs are delivered to the *data stream* of your choice in Amazon **Kinesis Data Streams**. 
	- You can build your own Kinesis data stream consumer, or **use Amazon Kinesis Data Firehose** to send the log data to *S3*, Redshift, OpenSearch Service *OpenSearch* Service, or a third-party log processing service.
	- ![[attachments/Pasted image 20230301093015.png]] 
- Note that enabling *real-time logs* incurs **additional charges for data transfer** and storage, so it's important to monitor and manage your usage to avoid unexpected costs.

## CloudFront functions
- *Lightweight functions* written in **JavaScript**.
- For high-scale, latency-sensitive CDN customisations.
- Sub-ms startup times, **millions of requests/second**.
	- With lambda@edge we can only have 1000s of requests per second.
- Used to change **Viewer requests and responses**:
	- Viewer Request: after CloudFront receives a request from a viewer
	- Viewer Response: before CloudFront forwards the response to the viewer

> [!caution]+ CloudFront functions CANNOT change origin request and response. 
> If we want to change origin request and response then we should use [[../101 AWS SAA/Lambda#@Edge | lambda@edge]].

- Native feature of CloudFront (*manage code entirely within CloudFront*)
- Difference between CloudFront functions and lambda@edge
	- ![[attachments/Pasted image 20230301175021.png]]