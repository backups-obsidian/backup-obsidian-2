---
created: 2022-09-15 21:09
updated: 2023-05-05 12:18
---
---
**Links**: [[111 KodeCloud Index]]

---
## Jobs
- *Behaviour of a short temporary work load in docker*:
	- Docker container finishes the job and then exits.
	- When we run the `docker ps` command we can see the container in an exited state with the status code of the operation.
		- ![[attachments/Pasted image 20220926173043.png]]
- *Behaviour of a short temporary work load in k8s*:
	- A pod is created and it does the job. 
	- The pod goes into the completed state. 
	- But *k8s pod then recreates the container in an attempt to leave it running*.
	- This happens a fixed number of times.
- This happens since *k8s wants our applications to live forever*. 
- **The default behaviour of the pods is to restart the container in an attempt to leave it RUNNING**.
- This behaviour is defined by the `restartPolicy` set on the pod whose default value is **`Always`**.
	- This is why the pod always recreates the container when the container exits.
	- We can override this property by setting it to **`Never`** or **`OnFailure`**.
	- The Job resource DOES NOT support `restartPolicy: Always` since it won't make any sense.
- Now suppose you have a batch processing job in which you want a lot of pods.
	- We need a job manager.
	- A ReplicaSet is needed to ensure a specified number of pods is running at each time *a Job is used to perform a given task to completion*. 
	- We do it using the **`Jobs` resource**.
- Working with jobs:
	- ![[attachments/Pasted image 20220926173856.png]]
	- We see that the pod exits after successfully completing the job and is not restarted.
	- Once the job is completed both the pod and the job resource won't be deleted.
		- We will have to explicitly delete them.
		- The pod will remain in a **`COMPLETED` status** after the completion of the job.
		- The *reason why pod is NOT deleted is so that we can query its logs if needed after the completion of the job*.
		- If we delete the job then it will also delete the pod.
- Using multiple pods in a job:
	- ![[attachments/Pasted image 20220926174133.png]]
	- In the above example 3 (since completions is 3) pods will created one after another.
		- The same work will be done in each pod.
- *Completions here refer to the number of successful completions that is required from the job*.
	- Job resource *ensures that the desired number of completions is achieved*.
	- If we delete a pod while it was performing a job, Job resource will create a new pod to achieve the desired number of completions.
- **By default pods are created one after another (Sequentially)**.
	- So this means if you have a pod which sleeps of 1 minute and you use `completions: 3`, then it will take 3 minutes for the job to finish.
	- So *if one fails a new pod is created and jobs are attempted until we have the desired number of completions*.
	- ![[attachments/Pasted image 20220926174243.png]]
- **We can do the jobs in parallel using the `parallelism` field**.
	- We can specify how many pods should run at the same time `parallelism: <number>`
- If due to some error the pod keeps failing then k8s will keep on trying (recreating it) with an exponential backoff.
	- The number of retries is specified in the `backoffLimit` setting.
- We can set an `activeDeadlineSeconds` after which the job will fail if it takes more time.
- **Use Cases**:
	- *One time initialization of resources* such as Databases.
	- Multiple workers to *process messages in queue* (batch processing).

## Cron Jobs
- A cron job is a job that can be scheduled and run periodically.
	- ![[attachments/Pasted image 20220926174638.png]]
- Assuming that the cron job is scheduled every minute, then every minute the cron job will create a new job and that job will create new set of pods.
	- ![[attachments/Pasted image 20230505120548.png]]
- By default cron job keeps last 3 success job (`successfulJobsHistoryLimit`) and last 1 failed job (`failedJobsHistoryLimit`).
- We can suspend cron jobs thereby preventing to schedule any future jobs and pods.
	- We can just edit the yaml file with `suspend: true`
- We can define concurrency policies for cron jobs.
	- By default it is `concurrencyPolicy: Allow`
	- `concurrencyPolicy: Forbid` only spawn one job at a time. Wait for that job to complete and then schedule a new one.
- **Use cases**:
	- Periodic backups of mysql database and so on.
		- So instead of having cron jobs in OS inside containers we can use k8s cron jobs.

## References
- [[ Kube 11 ] Jobs & Cronjobs in Kubernetes Cluster - YouTube](https://www.youtube.com/watch?v=uJKE0d6Y_yg)