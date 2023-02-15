---
created: 2022-05-15 08:57
updated: 2023-02-15 10:00
---
---
**Links**: [[102 AWS DVA Index]]
**Recommended Reads**: [[../101 AWS SAA/CloudFront | CloudFront]]

---
## Caching
- *Cache* based on:
	- **Headers**
	- **Session Cookies**
	- **Query String Parameters**

- You can't cache based on HTTP Methods.
- The cache lives at each CloudFront Edge Location
- You want to *maximise the cache hit rate to minimise requests on the origin*. In case of cache miss the request is forwarded to the origin.
	- ![[attachments/Pasted image 20220515090054.png]]

- We use *TTL to control the cache hit*. TTL (0 seconds to year), can be set by the origin using the Cache *Control header*, *Expires header*.
- We can invalidate a part of the cache using the `CreateInvalidation` API
- In CloudFront it is common to separate caching of dynamic content from static content. 
	- For caching of static content we don't use any caching rules to maximise the cache hit. We use these rules for dynamic content.
	- ![[attachments/Pasted image 20220515090513.png]]

- By *default* the S3 content is cached for *1 day*. 
- We can define the cache policy while creating the distribution or after creating it (behaviours section). We can also make our own caching policy if we don't like the ones predefined by AWS.
- If you want to **invalidate the cache then go to the invalidations tab and put a wildcard (\*)**.

> [!question]- A website is being delivered using Amazon CloudFront and a Developer recently modified some images that are displayed on website pages. Upon testing the changes, the Developer noticed that the new versions of the images are not displaying. What should the Developer do to *force the new images to be displayed*?
> *Invalidate the old versions* of the images on the edge caches.
> ---
> If you need to remove a file from CloudFront edge caches before it expires, you can do one of the following
> - **Invalidate the file** from edge caches. We use wildcard characters to invalidate the cache.
> - **Use file versioning** to serve a different version of the file that has a different name.

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