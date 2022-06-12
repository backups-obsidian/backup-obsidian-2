---
created: 2022-05-01 11:23
updated: 2022-05-16 10:22
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/ElasticBeanstalk DVA | ElasticBeanstalk DVA]]

---
## ElasticBeanstalk
- It is a *developer centric view* for **deploying applications**.
- **Beanstalk is free** but we pay for the instances and the services that it instantiates.
- **AWS Elastic Beanstalk** supports the **deployment of web applications from Docker containers**
- Simply upload your code and Elastic Beanstalk automatically handles the deployment, *from capacity provisioning, load balancing, auto-scaling to application health monitoring*. 
- At the same time, *you retain **full control over the AWS resources** powering your application and can access the underlying resources at any time*. 
- *There is no additional charge* for Elastic Beanstalk - you pay only for the AWS resources needed to store and run your applications.
- We can map Elastic Beanstalk to an **alias in route53**.
- It is a **managed** service.
- We can have **different versions** of our application.
- AWS Elastic Beanstalk stores your *application files* and optionally, *server log files* in **S3**.

### Web vs Worker Tier
- Web environment scales on the basis of load 
	- ![[attachments/Pasted image 20220501112838.png]]
- Worker environment scales on the basis of **SQS messages**
	- ![[attachments/Pasted image 20220501112910.png]]
- We can combine the web and worker environment together by *pushing messages from web environment to worker environment*.