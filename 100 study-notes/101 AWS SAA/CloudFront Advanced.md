---
created: 2022-04-19 16:22
updated: 2022-04-24 11:57
---
---
**Links**: [[101 AWS SAA Index]]

---
## CloudFront Signed URL/Cookies
- **Use Case**: Give *access to premium shared content* to people all over the world.
- **Signed URL** gives **access** to only **one file**. One URL per file.
- **Signed Cookies** give access to **multiple files**. One signed cookie for many files.
- For *signed cookies* you *don’t have to change URL*.

- When we create a URL or cookie we need to specify the following:
	- URL expiration
	- *IP ranges to access the data from*
    - Trusted signers (which AWS accounts can create signed URLs)

- You create an application which generate the CloudFront signed URL using the SDK and then return it to the user. Now the user can access the content.

> [!caution] If you *bucket is not public* then CloudFront Signed URL is the only way to distribute cached premium content.

- S3 pre signed URLs have a limited lifetime, CloudFront Signed URLs can be valid for years.

## Pricing (Edge Locations)

- CloudFront **pricing** varies **depending on the data out per edge location**. Pricing is different for different edge locations.
- We can **reduce** the **edge locations** for **price reduction**.
- There are different price classes for price reduction. By *default its All*.
	- **All** : Includes all regions and offers *best performance*.
	- **200** : most regions, but excludes the most expensive regions
	- **100** : *only the least expensive region*.

## Routing to different origins
- In CloudFront we *can define multiple origins*. We can *route to different origins* depending on the **content type**.
	- ![[attachments/Pasted image 20220424115007.png]]

## CloudFront Origin Failover
- You can also set up CloudFront with *automatic origin failover* for scenarios that require high availability. An origin group may contain **two origins**: a primary and a secondary. If the primary origin is unavailable or returns specific HTTP response status codes that indicate a failure, CloudFront automatically switches to the secondary origin.

> [!tip] To set up *origin failover*, you must have a distribution with **at least two origins**.

> [!question]- A website uses a CloudFront web distribution to serve their static contents to their millions of users around the globe. There are occasions when their users are getting HTTP 504 errors. How would you solve it?
> Set up an origin failover by *creating an origin group with two origins*. Specify one as the primary origin and the other as the second origin which CloudFront automatically switches to when the primary origin returns specific HTTP status code failure responses.

- In case of *S3* you can do a *CRR* of the primary origin bucket and *make it the secondary origin*.
	- ![[attachments/Pasted image 20220424115209.png]]

## Field Level Encryption
- **Protect sensitive user information** like PII (Personally Identifiable Information).
- Custom application logic on the *web server to decrypt the logic*
- Adds an additional layer of security *along with HTTPS*.
- *Sensitive information encrypted at the edge* close to user.
- **Usage**:
	- Specify set of *fields in POST requests* that you want to be encrypted (up to 10 fields)
	- Specify the *public key to encrypt them*
	- ![[attachments/Pasted image 20220424115442.png]]


