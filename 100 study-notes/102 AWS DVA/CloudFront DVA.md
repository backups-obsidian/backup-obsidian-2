---
created: 2022-05-15 08:57
updated: 2022-06-06 10:14
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
- If you want to invalidate the cache then go to the invalidations tab and put a wildcard (\*).
- Deploying your website using CLI
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
	- Redirect HTTP to HTTPS
	- Or use HTTPS only
- **Origin Protocol Policy**: (HTTP or S3):
	- HTTPS only
	- Or Match Viewer: (HTTP => HTTP & HTTPS => HTTPS)

> [!note] [[../101 AWS SAA/S3 Websites#Websites| S3 Websites]] don't support HTTPS but if you put your S3 website behind a CloudFront distribution then it will be secured by HTTPS.

## CloudFront Signed URL Process
- More about [[../101 AWS SAA/CloudFront Advanced#CloudFront Signed URL Cookies | CloudFront Signed URLs]]
- *Two types of signers*:
	- **Trusted key group** (*recommended*)
		- Can leverage APIs to create and rotate keys (and IAM for API security)
	- An AWS Account that contains a *CloudFront Key Pair*
		- Need to *manage keys using the root account* and the AWS console
		- **Not recommended** because you shouldn't use the root account for this
- You **generate your own public / private key** 
	- The *private key* is used by your *applications* (e.g. EC2) to *sign URLs*
	- The *public key* (uploaded) is used by *CloudFront to verify URLs* 
- After creating the keys we add the public keys in our CloudFront distribution and then add the public keys to key groups.
- In our CloudFront distribution, we *can create one or more trusted key groups*.