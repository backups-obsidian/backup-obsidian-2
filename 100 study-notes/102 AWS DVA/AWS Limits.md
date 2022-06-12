---
created: 2022-05-14 16:43
updated: 2022-06-06 10:07
---
---
**Links**: [[102 AWS DVA Index]]

---
## API Rate Limits
- For eg: `DescribeInstances` API for EC2 has a limit of 100 calls per seconds
- `GetObject` on S3 has a limit of 5500 GET per second per prefix
- For **Intermittent Errors**: implement *Exponential Backoff*.
- For **Consistent Errors**: *request an API throttling or limit increase*.

### Exponential Backoff
- Exponential backoff means that we double the waiting time before each retry.
- If you get **ThrottlingException** *intermittently*, use exponential backoff
- Retry mechanism *already included in AWS SDK API calls*
- Must implement yourself if using the AWS API as-is or in specific cases.

> [!important]+ Must  implement the retries on **5xx server errors** and **throttling**
> Retries *should not implemented on the 4xx client errors* since this means *we are sending something wrong*. So we will always get the same error on retry.

## Service Limits (Service quotas)
- For eg: Running On-Demand Standard Instances have a service limit of 1152 vCPU
- Service limits can be increased in 2 ways:
	- You can request a service limit increase by *opening a ticket*
	- You can request a service quota increase by using the *Service Quotas API*
