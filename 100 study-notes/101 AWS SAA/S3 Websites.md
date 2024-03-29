---
created: 2022-04-19 16:22
updated: 2023-03-13 09:05
---
---
**Links**: [[101 AWS SAA Index]]

---
## Websites 
- S3 can *host websites* and make them accessible to the public internet.
- It can be used to **distribute static content**.
- The URLs can be different depending on the region.

> [!caution] Bucket name should be *same* as the domain or subdomain name.

- We can use referrer policy in the bucket policy that allows `s3:GetObject` permission with a condition, using the `aws:referer` key, that the get request must originate from specific webpages. 

### Steps to host a static website
- We need to **enable static website** hosting for the bucket by going to **properties**. Once enabled we will get a URL. If we try to access this now we will get 403 error.
- If you get a **403 (Forbidden)** error then make sure the *bucket policy allows public reads*.
- We need to **allow public access** in permissions
- **Allow public read access** using **bucket policies** for **all (`arn/*`) the objects in the bucket** (without this also we will get **403** errors)
	- ```json
	{
		"Version": "2012-10-17",
		"Id": "Policy1646646068967",
		"Statement": [
			{
				"Sid": "Stmt1646646067735",
				"Effect": "Allow",
				"Principal": "*",
				"Action": "s3:GetObject",
				"Resource": "arn:aws:s3:::hugahugalulu/*"
			}
		]
	}

- After and before the bucket policy
	- ![[attachments/Pasted image 20220423104800.png]]

- Upload an **index document** and enter the name of the index document when enabling static website hosting.

## CORS
- CORS stands for **cross origin resource sharing** which means you want to get resources from other origins
- For **same origin** **protocol**, **host** and **port** should be same.
- **Web browsers** enforce this. This means you **can make requests to other origin** only **if** the **other origin allows this** using the CORS header (ex: `Access-Control-Allow-Origin`).

- Same and different origin
	- ![[attachments/Pasted image 20220423104911.png]]

- We always do a preflight first to determine if our origin is allowed. If allowed then what methods are allowed.
	- ![[attachments/Pasted image 20220423104949.png]]


### S3 CORS
- It is an **XML document**.
- If a client does a cross origin request on our S3 bucket *we need to enable CORS headers*. If not enabled then the request will not go through.
	- ![[attachments/Pasted image 20220423105230.png]]
- We can allow a specific origin or all the origins (\*)
- The CORS headers have to be **defined on the cross origin bucket** and not the website bucket.

- A CORS configuration is an XML file that contains a series of rules within a `<CORSRule>`.
	- `AllowedOrigin` - *Specifies domain origins* that you allow to make **cross-domain requests**.
	- `AllowedMethod` - Specifies a type of *request you allow (GET, PUT, POST, DELETE, HEAD)* in **cross-domain requests**.
	- `AllowedHeader` - *Specifies the headers* allowed in a **preflight request**.

- Below are some of the `CORSRule` elements: 
	- `MaxAgeSeconds` - Specifies the *amount of time in seconds* that **the browser caches an Amazon S3 response to a preflight OPTIONS** request for the specified resource. 
		- By caching the response, the browser does not have to send preflight requests to Amazon S3 if the original request will be repeated.
	- `ExposeHeader` - *Identifies the response headers  that customers are able to access from their applications*.

- If you allow CORS access to a domain it *allows a user to view, add, remove or update objects (depending on the rule) inside the S3 bucket* from the allowed domain.

 > [!question]- A digital marketing company has its *website* hosted on an Amazon S3 *bucket A*. The development team notices that the *web fonts* that are hosted on another S3 *bucket B* are not loading on the website.
 > Configure **CORS on the bucket B** that is hosting the web fonts *to allow Bucket A origin to make the requests*.