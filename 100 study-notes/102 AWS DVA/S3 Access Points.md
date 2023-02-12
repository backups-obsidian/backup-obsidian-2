---
created: 2023-02-11 18:30
updated: 2023-02-11 18:55
---
---
**Links**: [[102 AWS DVA Index]]

---
## Access Points
- Suppose we have users from different departments and they want to access different buckets.
	- We can have complex bucket policies to enable this but this would become difficult to manage.
- We *push the security management from S3 bucket policies to the access point*.
	- Each access point will have its own security
- By using **access points we define different ways to access our bucket**. 
	- ![[attachments/Pasted image 20230211184409.png]]
- We have *policies attached to each access point*.
- Because of access points we have a very simple bucket policy.
	- It simplifies security to our bucket.
- Each *Access Point* has:
	- Its **own DNS name** (Internet Origin or VPC Origin)
	- An **access point policy** (*similar to bucket policy*) - manage security at scale 

### S3 Access Points - VPC Origin
- We can define the *access point to be accessible only from within the VPC*.
- You must **create a VPC Endpoint** to access the Access Point (Gateway or Interface Endpoint)
- The VPC Endpoint Policy must allow access to the target bucket and Access Point 

> [!note]+ In total to use this there are 3 policies (*VPC Endpoint policy*, *S3 Access Policy*, and *S3 Bucket Policy*)
> ![[attachments/Pasted image 20230211184752.png]]

### S3 Object Lambda
- It uses S3 Access points.
- Use AWS Lambda Functions to **change the object before it is retrieved by the caller application**.
	- This means there is no need of having multiple versions of our data in different buckets.
	- *Only one S3 bucket is needed*, on top of which we create **S3 Access Point** and **S3 Object Lambda Access Points**.
- Use Cases:
	- *Redacting personally identifiable information* for analytics or non-production environments.
		 - ![[attachments/Pasted image 20230211185434.png]]
	- Converting across data formats, such as converting XML to JSON.
	- Resizing and watermarking images on the fly **using caller-specific details**, such as the user who requested the object.
