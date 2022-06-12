---
created: 2022-04-18 18:55
updated: 2022-04-18 19:05
---
---
**Links**: [[101 AWS SAA Index]]

---
- Most AWS services are region scoped
- **Global** Services: **Route53**, **IAM**, **CloudFront**
- All services may not be present in one region so switch to another region if you want to use that service.
- Key factors which determine which region to choose: **GDPR** (compliance), **latency**, **services**, **pricing**. 
- Regions have a codes like `us-east-1`, `eu-west-3`. Each region has a code mapped to a name. Like `ap-south-1` is Mumbai.
- No of AZs in a region: 2 (min), **3** (common) or 6 (max).
- Each (AZ) has *one or more discrete data centres*.
- The AZs are separated from each other so that they are isolated from disasters. *No cascading effect*.
- Edge locations are also known as PoP (*Points of presence*)
- Notice that the region code is `ap-southeast-2` and the codes for AZs are `ap-southeast-2a,2b and 2c`.
