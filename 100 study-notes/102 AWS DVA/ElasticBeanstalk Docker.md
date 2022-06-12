---
created: 2022-05-16 15:13
updated: 2022-05-16 15:20
---
---
**Links**: [[102 AWS DVA Index]]

---
## Using Docker
### Single Container
- Run your application as a single docker container
- Either provide:
	- `Dockerfile`: Elastic Beanstalk *will build and run* the Docker container
	- `Dockerrun.aws.json (v1)`: Describe where **already built** docker image is along with the other essential details like ports, volumes etc and Beanstalk will deploy the container for us on EC2 instances.

> [!note] Beanstalk in Single Docker Container **does not use ECS**.

### Multi Container
- Multi Docker helps *run multiple containers per EC2* instance in EB
	- ![[attachments/Pasted image 20220516152016.png]]
- This will *create* for you:
	- *ECS Cluster*
	- EC2 instances, configured to use the ECS Cluster
	- *Load Balancer* (in high availability mode)
	- Task definitions and execution
- Requires a config `Dockerrun.aws.json (v2)` at the *root of source code*
- `Dockerrun.aws.json` is used to *generate the ECS task definition*. 
- Your **Docker images must be pre-built** and stored in ECR for example.
