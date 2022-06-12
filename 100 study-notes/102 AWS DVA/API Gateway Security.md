---
created: 2022-05-16 12:39
updated: 2022-05-27 09:43
---
---
**Links**: [[102 AWS DVA Index]]

---
## IAM Permissions
![[../101 AWS SAA/API Gateway#IAM]]

## Resource Policies
- Resource policies (similar to Lambda Resource Policy)
- Allow for **Cross Account Access** (combined with IAM Security)
- Allow for a *specific source IP address*
- Allow for a *VPC Endpoint*

## Cognito User Pools
![[../101 AWS SAA/API Gateway#Cognito User Pools]]

## Lambda/Custom Authoriser
![[../101 AWS SAA/API Gateway#Lambda Custom Authoriser]]

## Summary
- **IAM**:
	- Great for *users roles already within your AWS account*, + resource policy for cross account
	- Handle *authentication + authorisation*
	- Leverages Signature v4
- **Custom Authoriser**:
	- Great for *3rd party tokens*
	- *Very flexible in terms of what IAM policy is returned*
	- Handle Authentication verification + Authorisation in the Lambda function
	- Pay per Lambda invocation, *results are cached*
- **Cognito User Pool**:
	- You manage your own user pool (can be backed by Facebook, Google login etc...)
	- *No need to write any custom code*
	- Must implement authorisation in the backend