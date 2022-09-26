---
created: 2022-09-15 21:09
updated: 2022-09-26 17:52
---
---
**Links**: [[111 KodeCloud Index]]

---
## Jobs
- Short temporary work load in docker:
	- Docker container finishes the job and then exits.
	- When we run the `docker ps` command we can see the container in an exited state with the status code of the operation.
		- ![[attachments/Pasted image 20220926173043.png]]

- Short temporary work load in k8s:
	- A pod is created and it does the job. 
	- The pod goes into the completed state. 
	- But k8s then recreates the container in an attempt to leave it running.
	- This happens a fixed number of times.
	
- This happens since *k8s wants our applications to live forever*. **The default behaviour of the pods is to restart the container in an attempt to leave it RUNNING**.
- This behaviour is defined by the `restartPolicy` set on the pod whose default value is always.
	- This is why the pod always recreates the container when the container exits.
	- We can override this property by setting it to never or on failure.

- Now suppose you have a batch processing job in which you want a lot of pods.
	- We need a job manager.
	- A ReplicaSet is needed to ensure a specified number of pods is running at each time *a Job is used to perform a given task to completion*. 

- Working with jobs:
	- ![[attachments/Pasted image 20220926173856.png]]
	- We see that the pod exits after successfully completing the job and is not restarted.
- Using multiple pods in a job:
	- ![[attachments/Pasted image 20220926174133.png]]
- *Completions here refer to the number of successful completions that is required from the job*.
- *By default pods are created one after another*. So if one fails a new pod is created and jobs are attempted until we have the desired number of completions
	- ![[attachments/Pasted image 20220926174243.png]]
- **We can do the jobs in parallel using the `parallelism` field**.

## Cron Jobs
- A cron job is job that can be scheduled and run periodically.
	- ![[attachments/Pasted image 20220926174638.png]]