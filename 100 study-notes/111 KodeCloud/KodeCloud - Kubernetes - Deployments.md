---
created: 2022-10-10 15:34
updated: 2022-10-13 10:50
---
---
**Links**: [[111 KodeCloud Index]]

---
## Deployments/Rollouts
- Deployment vs Replica Set
	- ![[attachments/Pasted image 20220919193045.png]]

- **Components of the deployment definition file are exactly same as the Replica Set definition file except for the kind**.

> [!question]+ What are the additional features provided by Deployment over ReplicaSet considering they have the same definition file.
> - You don't want to upgrade all the instances instantly. You would want *rolling updates*. 
> - *Roll back the changes*.
> - Make multiple changes to your environment. You wouldn't want to apply it immediately. You would like to *pause the environment* make the changes and *resume* so that all the changes are rolled out together.

- The deployment automatically creates a ReplicaSet
- Pod naming convention when created by deployment: `<deployment-name>-<replica-set-hash>-<random-hash>`
- We can see the **status of roll out** using: 
	- `k rollout status deployment/<deployment-name>`
- Get the **history and revisions** of the rollout: 
	- `k rollout history deployment/<deployment-name>`
- Get the rollout history of a particular revision: 
	- `k rollout history deployment/<deployment-name> --revision=3`
- *Rolling update is the default deployment strategy*.
- Another deployment strategy is *Recreate* in which all the pods are first destroyed and then the new revision is added.

- Difference between Recreate & Rolling Update: 
	- ![[attachments/Pasted image 20220919202752.png]]
	- See how the ReplicaSet replicas change in Recreate (*reduced to 0*) and Rolling Update (few units at a time)
- **When we apply a deployment definition a new rollout is triggered and a new revision is created**.

> [!question]- How do upgrades happen in Deployments?
> - When we upgrade our application *a new ReplicaSet is created under the hood and starts deploying the containers there*. 
> - At the same time pods are being taken down in the old ReplicaSet in a rolling update strategy.
> ![[attachments/Pasted image 20220920000658.png]]
> - This can be verified when we list the ReplicaSets. We will see one ReplicaSet with 0 pods and other one with x pods.

- We can easily rollback using `k rollout undo deployment/<deployment-name>`
	- This will destroy the pods in the new ReplicaSet and bring the older ones up in the old ReplicaSet.
	- ![[attachments/Pasted image 20220920000907.png]]
	- We can notice the difference in ReplicaSets before and after the rollback.
- Rollback to a particular revision:
	- `k rollout undo deployment/<deployment-name> --to-revision=1`

- Whenever we apply a deployment file we can record the cause of rollout using the `--record` flag.
	- `k apply -f deployment.yaml --record`
	- Using this would mean that the cause of change is tracked in the revision history.

- *Scenario*: In the deployment you change the image to a non existent image
	- When you apply this configuration you will have some working pods (from the older ReplicaSet) and some non working pods (from the new ReplicaSet)
	- ![[attachments/Pasted image 20220920233119.png]]
	- In the above example image we see that only one pod was terminated (from the older ReplicaSet) and it failed to create 3 new pods since it couldn't pull the image.
	- You can notice the different ReplicaSet by comparing the hash.
	- **Even though we have specified the wrong image, the application is not impacted** since we have 5 working pods.
	- This is because of the default deployment strategy of rolling deployment.
	- Kubernetes will only delete the older pods once all the new pods are up and running.
