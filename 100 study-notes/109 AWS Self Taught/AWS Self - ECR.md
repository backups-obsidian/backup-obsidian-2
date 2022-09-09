---
created: 2022-09-08 11:54
updated: 2022-09-09 14:32
---
---
**Links**: [[109 AWS Self Taught Index]]

---
## ECR
- First step is to create a repository
	- `aws ecr create-repository --repository-name <repository-name> --image-scanning-configuration scanOnPush=true --region <region>`
	- We can *include slashes in repo name* for eg: `company-name/<something>`
- Once you create a repo you can push images in it using the repository URI 
	- `<account-id>.dkr.ecr.<region>.amazonaws.com/<repo-name>`
- Describing the repositories to see all the repositories
	- `aws ecr describe-repositories -region <region>`
- Building docker images
	- `docker build -t image-name .`: assuming `Dockerfile` is in the same directory.

- To push the docker image to ECR we *need to login*
	- If you login successfully you should see Login Succeeded message
- **We need to tag the docker images with the same name as the repository with version**
	- `docker tag <image-name> <account-id>.dkr.ecr.<region>.amazonaws.com/<repo-name>:1`
	- We do this in order to tell docker that we want this image to be pushed to ECR
	- Tag basically means that we are renaming the image.
	- Here 1 is the version number we can also have `latest` or any other version

> [!caution]+ In ECR *you create a repository per image*.
> - For each image you have its own repository.
> - We store different tags/versions of the same image in our repository. 

- Image naming in ECR:
	- `registryDomain/imageName:tag`
- We can do `docker pull nginx:latest` since it is equivalent to `docker pull docker.io/library/nginx:latest`
	- But we can't skip this in private repositories.
		- ![[attachments/Pasted image 20220909142849.png]]


## References
- [Private Repository explained | Registry on AWS - Docker in Practice || Docker Tutorial 11 - YouTube](https://www.youtube.com/watch?v=vWSRWpOPHws)