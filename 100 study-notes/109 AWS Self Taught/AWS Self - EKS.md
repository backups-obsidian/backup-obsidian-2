---
created: 2022-08-10 17:17
updated: 2022-08-10 18:25
---
---
**Links**: [[109 AWS Self Taught Index]]

---
## Elastic Kubernetes Service
### Basic Kubernetes Architecture
![[attachments/Pasted image 20220810175326.png]]

### Control Plane & Worker Nodes
> [!important]- It is a **managed Kubernetes cluster** which means AWS will *manage the master nodes (control plane)* for us.
> - The control plane is running in an **AWS provisioned VPC and NOT the customer controlled VPC**.
> - AWS manages everything about the control plane like backup, scaling, installing the required software etc. It does all the heavy lifting.
> - This ensures that we can focus on deploying our applications instead of managing the platform
>
> ![[attachments/Pasted image 20220810175420.png]]

- We *only* create the **worker nodes** and connect them to the cluster.
	- So the data plane/worker nodes runs in the customer VPC.
	- Worker nodes are nothing but EC2 instances with some CPU, RAM etc.

### How to create a simple cluster
- We create **node groups** (not single EC2 instances) and add them to the cluster. 
- We also have to define the Security Group, create roles for eks control plane and worker nodes, select instance type, resources etc.
- With node groups we also have autoscaling and for this we define the max and min number of nodes.
- We connect to the cluster from our local machine using `kubectl`
- Diagrammatic representation of the flow
	- ![[attachments/Pasted image 20220810175616.png]]

> [!note]- This is a lot of effort for creating a simple Kubernetes cluster. We can simplify cluster creation using `eksctl` (`eks create cluster`).
> - It will create a cluster with default values. These parameters can be overridden.
> - This is *community tool*.

### Miscellaneous
- When using EKS we can either go with *EC2 machines (node groups) or Fargate (Fargate Profiles)*.
	- ![[attachments/Pasted image 20220810181119.png]]
- EKS can be integrated with a number of AWS services using add ons.

## References
- [Introduction to Amazon EKS Kubernetes for beginners - YouTube](https://www.youtube.com/watch?v=QThadS3Soig)
- [AWS EKS Tutorial | Kubernetes on AWS | Create EKS Step by Step for Beginners | K21Academy - YouTube](https://www.youtube.com/watch?v=DcnviAwmzM4)