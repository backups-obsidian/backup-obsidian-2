---
created: 2023-01-09 22:37
updated: 2023-01-09 23:10
---
---
**Links**: [[113 Terraform Index]]

---
## AWS
- For using our programmatic access credentials with AWS we can make use of `provider` in terraform.
	- ![[attachments/Pasted image 20230109223848.png]]
	- This is *not a recommended approach*.
- Instead we can have the credentials in `.aws/config/credentials` and terraform will automatically make use of those credentials.
	- ![[attachments/Pasted image 20230109224215.png]]
- Another way of *passing these variables is using the environment variables*.
	- We can also set the region in environment variables allowing us to remove the provider block completely
	- ![[attachments/Pasted image 20230109224252.png]] 

### Different ways of creating IAM policies with terraform
- Using `heredoc` syntax for the policy json
	- ![[attachments/Pasted image 20230109224552.png]]
- Attaching an IAM policy to an user
	- ![[attachments/Pasted image 20230109224715.png]]
- *Using file for policy json*
	- ![[attachments/Pasted image 20230109224934.png]]
- We can make use of *variable substitution in the heredoc syntax*
	- ![[attachments/Pasted image 20230109230939.png]]
-

