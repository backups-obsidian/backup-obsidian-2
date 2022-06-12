---
created: 2022-04-19 16:22
updated: 2022-04-23 10:53
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
- If you get a *403 (Forbidden)* error then make sure the *bucket policy allows public reads*.
- We need to **allow public access** in permissions
- **Allow public read access** using bucket policies for **all** (`arn/*`) the objects in the bucket (without this also we will get 403 errors)
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

## CORS
- CORS stands for **cross origin resource sharing** which means you want to get resources from other origins
- For **same origin** **protocol**, **host** and **port** should be same.
- **Web browsers** enforce this. This means you **can make requests to other origin** only **if** the **other origin allows this** using the CORS header (ex: `Access-Control-Allow-Origin`).

- Same and different origin
	- ![[attachments/Pasted image 20220423104911.png]]

- We always do a preflight first to determine if our origin is allowed. If allowed then what methods are allowed.
	- ![[attachments/Pasted image 20220423104949.png]]

### S3 CORS
- If a client does a cross origin request on our S3 bucket *we need to enable CORS headers*. If not enabled then the request will not go through.
	- ![[attachments/Pasted image 20220423105230.png]]
- We can allow a specific origin or all the origins (\*)
- The CORS headers have to be **defined on the cross origin bucket** and not the website bucket.
