---
created: 2022-04-19 16:22
updated: 2022-05-15 15:56
---
---
**Links**: [[101 AWS SAA Index]]

---
## ECR
-  It stands for **Elastic Container Registry**.
- It is used to **store and manage docker images** on AWS.
- We can store images *privately or publicly* on ECR.
- You pay for what you use.
- ECR is fully integrated with ECS and the **images are stored in S3**.
- EC2 instances will *pull the images from ECR* to create containers. It can do so because of *instance role* (EC2 instance profile) permissions.
- We use AWS CLI to push images to ECR.

> [!tip] Any permissions issues against ECR are most likely due to IAM permissions. For example *CodeBuild service must have the required permissions to push Docker images to ECR repositories*.

## EKS
- Stands for **Elastic Kubernetes Service**
- Launch **managed** **Kubernetes cluster** on AWS
- It has a similar goal as ECS but is **open source**. Kubernetes is *cloud agnostic*.
- It supports **2 launch modes**: **EC2** and **Fargate**
- It provides a **fully-managed control plane** so you would not have access to the EC2 instances that the platform runs on.
- EKS pods run on EKS nodes (EC2 instances or Fargate). Here **EKS pods are similar to ECS tasks**.
	- ![[attachments/Pasted image 20220515154659.png]]