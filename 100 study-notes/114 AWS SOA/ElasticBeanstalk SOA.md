---
created: 2023-03-02 21:29
updated: 2023-03-11 09:32
---
---
**Links**: [[114 AWS SOA Index]]

---
![[../101 AWS SAA/ElasticBeanstalk#ElasticBeanstalk]]
![[../102 AWS DVA/ElasticBeanstalk DVA#Components]]

- **Deployment modes**:
	- **Single instance**: great for *dev*, *one instance* with an *elastic IP*
	- **HA with Load Balancer**: great for *prod*, ALB, RDS with multi AZ standby
	- ![[attachments/Pasted image 20230302213138.png]]

> [!question]- An IT company runs its server infrastructure on Amazon EC2 instances configured in an Auto Scaling Group (ASG) fronted by an Elastic Load Balancer (ELB). For ease of deployment and flexibility in scaling, this AWS architecture is maintained via an Elastic Beanstalk environment. The Technology Lead of a project has requested to *automate the replacement of unhealthy Amazon EC2 instances* in the **Elastic Beanstalk environment**. How will you configure a solution for this requirement?
> - To automate the replacement of unhealthy EC2 instances, you must change the *health check type* of your instance's Auto Scaling group from **EC2 to ELB** by **using a configuration file of your Beanstalk environment (`.ebextensions)`**
> ---
> - *Configuration changes won't persist if it is directly changed from the console*.