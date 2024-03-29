---
created: 2022-04-23 10:36
updated: 2023-03-04 09:53
---
---
**Links**: [[101 AWS SAA Index]]

---
## Strongly consistent
- All S3 *GET, PUT, DELETE, and LIST* (**object** level) operations, as well as operations that change object tags, ACLs, or metadata, are *strongly consistent*. 
	- **What you write is what you will read**, and the results of a LIST will be an accurate reflection of what’s in the bucket.
- For example:  A process replaces an existing object and immediately tries to read it. Amazon S3 always returns the latest version of the object.

> [!caution]- **Bucket configurations have an eventual consistency model**. 
> If you delete a bucket and immediately list all buckets, the deleted bucket might still appear in the list.

## Scaling Requests
- Your application can achieve **at least 3,500 PUT/COPY/POST/DELETE** and **5,500 GET/HEAD requests per second per prefix in a bucket**.
- There is *no limit to the number of prefixes in a bucket*. Which means you can scale the requests infinitely.
	- If you create *10 prefixes* in an Amazon S3 bucket to parallelise reads, you could scale your read performance to *55,000 read requests* per second.
	- Different prefixes
	- ![[attachments/Pasted image 20220423150756.png]]

> [!question]- Customer requests are failing during peak hours since there are more than 5000 requests per second. How do you solve it?
> Change the application architecture to *create customer-specific custom prefixes* within the single bucket and then upload the daily files into those prefixed locations.

- Although you can increase the number of prefixes there is a chance that *SSE KMS can limit the performance* of S3 because of its limits depending on the region.
	- There is a KMS quota of number of requests per second per region
	- We would have to request a quota increase.

## Multi Part
- It is **recommend** to use multi part download for files **over 100mb**. **Mandatory** for files over **5Gb**
- **Multipart downloads happen in parallel** there by increasing the speed.
- It can be used for **uploads** also.
	- We can *parallelise the uploads*.
	- Max parts 10000
	- *Failures*: restart **uploading ONLY failed parts** (improves performance)
	- We can use **Lifecycle Policy** to *automate old parts deletion* of unfinished upload after x days.
	- We can *only use the CLI (`s3api`) or SDK*.

> [!question]- While you're uploading *large files to an S3 bucket using Multi-part Upload*, there are a lot of *unfinished parts stored in the S3 bucket due to network issues*. You are not using the unfinished parts and they cost you money. What is the best approach to remove these unfinished parts?
> Use an *S3 Lifecycle Policy* to automate old/unfinished parts deletion.

## Transfer Acceleration
- **Increase transfer speed** by *sending the file to AWS edge location* and then sending the file to S3 bucket in the target region.
- It *supports multi part download/upload*.
- We **minimise** the **public internet** and **maximise** the **private AWS network** we go through.
	- ![[attachments/Pasted image 20220423151313.png]]

- Users are from **different parts of the globe**.
- For Transfer Acceleration (S3TA), you pay only for transfers that are accelerated. If the acceleration fails you don’t pay for it.

> [!note] Transfer acceleration is the only case where you *pay for data going* into S3.

> [!tip] Go for transfer acceleration if you come across key words like *fast data transfer irrespective of user location*.

> [!important]+ When to choose Transfer Acceleration over CloudFront
> If you have objects that are *smaller than 1GB* or if the data set is less than 1GB in size, you should consider using Amazon CloudFront's PUT/POST commands for optimal performance. If the use case has *data larger than 1GB* then Transfer Acceleration is the better option.

> [!question]- A web application is uploading large files, which are over 4 GB in size, in an S3 bucket called *data.tutorialsdojo.com* every 30 minutes. You want to minimise the time required to upload each file. Which of the following should you do to *minimise upload time*?
> Multi part upload
> ---
> We *CANNOT use transfer acceleration* in this case since the bucket in the scenario won't be able to turn on this feature. Take note that the name of the **bucket used for Transfer Acceleration must be DNS-compliant and must not contain periods (".")**.

## Byte Range Fetches
- **Parallelising get requests** by requesting specific byte ranges.
- Can be **used to speed up downloads**
- Can be used to **retrieve only partial data** (for example the head of a file)

> [!note]- You can use *concurrent connections* to Amazon S3 to fetch different byte ranges from within the same object. **No need to go through object one by one**.
> This means *Create an application that will traverse the S3 bucket, issue a Byte Range Fetch for the first 250 bytes, and store that information in RDS* over *Create an application that will traverse the S3 bucket, read all the files one by one, extract the first 250 bytes, and store that information in RDS*