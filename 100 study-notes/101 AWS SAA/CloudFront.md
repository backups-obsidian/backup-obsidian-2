---
created: 2022-04-19 10:02
updated: 2023-03-11 08:59
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/CloudFront DVA | CloudFront DVA]], [[CloudFront Advanced]]

---
## Introduction
- AWS **CloudFront** is a **CDN**
- CloudFront is a **global service**.
- Improves **read performance** since content is *cached at edge*.
- **Over 200** edge locations.
- Since CloudFront uses edge locations it also takes advantage of AWS internal network.
	- ![[attachments/Pasted image 20220424105318.png]]

- It can *serve both dynamic and static content*. This is the best solution for *improving the performance of the website*.

> [!note]+ Controlling cache age using `Cache Control max-age`
> - You can control how long your objects stay in a CloudFront cache before CloudFront forwards another request to your origin. 
> - *Reducing the duration allows you to serve dynamic content*. 
> - *Increasing the duration* means your users get *better performance* because your objects are more likely to be served directly from the edge cache. A longer duration also *reduces the load on your origin*.
> - `The Cache-Control` and `Expires` headers control how long objects stay in the cache. 
> - The `Cache-Control max-age` directive lets you specify *how long (in seconds) you want an object to remain in the cache before CloudFront gets the object again from the origin server*. 
> - The *minimum expiration time* CloudFront supports is **0** seconds for web distributions and 3600 seconds for RTMP distributions.

- There are *certain content types that bypass the regional edge cache*, and go directly to the origin.
	- Proxy methods *PUT/POST/PATCH/OPTIONS/DELETE* go directly *to the origin*. *Except GET* everything.
	- *Dynamic content*, as determined at request time 

## CloudFront Origins
- **S3 bucket**
	- For distributing files and caching them at the edge
	- Enhanced security with CloudFront **Origin Access Identity (OAI)**
	- CloudFront can be *used as an ingress* (to **upload files to S3**)

> [!note] *Origin Access Control (OAC)* will be replacing OAI.

- **Custom Origin (HTTP)**
	- **Application Load Balancer**
	- **EC2 instance**
	- *S3 website* (must first enable the bucket as a static S3 website)
	- *Any HTTP backend you want*. It can also point to **on premises server**.

> [!caution] All the CloudFront origins must be **publicly** accessible.

- Remember there are *2 main origins*, HTTP and S3.

> [!note]- CloudFront can be **configured with multiple origins** to serve both static and dynamic content at low latency to global users.

### S3 Origin
- For the edge location to access the S3 bucket it uses an **OAI (origin access identity)**. 
	- It is an *IAM role for the CloudFront origin*.
- OAI makes sure that S3 bucket is *only accessed from CloudFront* and not using its public URL.

> [!question]+ How to make sure that S3 bucket is only accessed from CloudFront and not its public URL?
> Configure your CloudFront Distribution and create an Origin Access Identity, then **update your S3 Bucket Policy to only accept requests from your CloudFront Distribution OAl user**.
> ---
> ![[attachments/Pasted image 20230305173422.png]]

- Apart from caching S3 content it also *provides HTTPs*.
	- You can create a static website using Amazon S3 with a custom domain name. However, *you cannot connect to an Amazon S3 static website using HTTPS (only HTTP)* 

> [!note] If your Amazon S3 bucket is configured as a website endpoint, *you CANT configure CloudFront to use HTTPS to communicate with your origin because Amazon S3 DOESNT support HTTPS connections in that configuration*.

### EC2/ALB as origin

- *EC2 instances* must be **public**.
- For the edge locations to access EC2 instances/ALB's the *security group* of the EC2/ALB must *allow the public IP of all the edge locations*.
	- ![[attachments/Pasted image 20220424110232.png]]

> [!question]- How to restrict access to content in EC2/ALB?
> You can use an OAI to restrict access to content in Amazon S3 *but not on EC2 or ELB*.
> ---
> The only way to get this working is by using a VPC **Security Group** for the ELB that is configured to *allow only the internal service IP ranges associated with CloudFront*. As these are **updated from time to time**, you can use *AWS Lambda to automatically update the addresses*. This is done using a trigger that is triggered when AWS issues an SNS topic update when the addresses are changed.

## CloudFront Georestriction
- You can *restrict who can access your distribution* depending on the **country of request**.
	- **Whitelist**: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
	- **Blacklist**: Prevent your users from accessing your content if they're in one of the countries on a blacklist of banned countries.
	- Allow and block list example
		- ![[attachments/Pasted image 20220515104749.png]]
	
- The **country** is determined using a 3rd party Geo-IP database
- *Use case*: **Copyright Laws** to control access to content
	- For example, if a request comes from a country where, for copyright reasons, you are not authorised to distribute your content, you can use CloudFront geo restriction to block the request. This is the *easiest and most effective way to implement a geographic restriction for the delivery of content*.

> [!tip] If you see keywords like *easiest* and *copyright laws* then go for CloudFront Georestriction.

## Difference between S3 CRR and CloudFront
- *CloudFront* is **great for static content** that is **available everywhere**.
- *S3 CRR* is great for **dynamic content** that needs to be available at *low latency in **some** regions*.
- *S3 CRR must be setup for each region* you want replication to happen whereas CloudFront is automatically setup for all the edge locations.

