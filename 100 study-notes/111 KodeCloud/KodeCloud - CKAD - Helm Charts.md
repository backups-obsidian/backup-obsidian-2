---
created: 2022-09-15 21:09
updated: 2022-10-04 00:14
---
---
**Links**: [[111 KodeCloud Index]]

---
## What are Helm Charts
- **Helm** is also known as a **package manager for k8s**.
- We use a single command to install our application even if it needs hundreds of objects.
	- `helm install <app-name>`
- We can customise the settings we want for our app by specifying desired values at install time (`values.yaml`).
	- But instead of editing multiple values in multiple yaml files we have a single location where we can declare every custom setting.
- We can upgrade our application with a single command: `helm upgrade <app-name>`
- We can roll back to the previous revision using: `helm rollback <app-name>`
- Uninstalling the app: `helm uninstall <app-name>`
	- *It keeps track of all the objects used by each app so it knows what to remove*.
	- We don't need to remember each object that belongs to our app anymore.

> [!note]- Helm lets us treat our app as an app rather than a collection of objects.
> This means that we don't have to micro manage the k8s objects.

- Running helm in verbose mode: `helm --debug`
- *Helm has a CLI client installed on our local machine* that helps us perform helm specific actions against our k8s cluster.

## Difference between Helm 2 & Helm 3
- There were some significant changes between helm 2 and helm 3.
- Helm 1 (2016) -> Helm 2 (2016) -> Helm 3 (2019)
- When Helm 2 was around k8s lacked features like RBAC, CRDs
	- To allow helm to do its magic an extra component called *Tiller* had to be installed in the k8s cluster.
	- Helm CLI communicated with Tiller and Tiller then communicated with the cluster.
	- Tiller also operated in God mode which posed security concerns.
	- ![[attachments/Pasted image 20221003233736.png]]

- **With the addition of RBAC and CRDs Tiller was removed from Helm 3**.

- Helm 3 uses 3 way strategic merge: 
	- This means *Helm 3 also takes into consideration the live state when performing rollbacks*.
		- ![[attachments/Pasted image 20221003234852.png]]
	- Helm 2 only considered the previous chart and the current chart when performing rollbacks.

## Helm components
- **Charts are a collection of files**. 
- They contain all the instructions helm needs to know to be able to create the collection of objects.

- **When a chart is applied to our cluster a release is created**.
	- A release is a single installation of an application using a helm chart.
	- Within each release we can have multiple revisions.
- **Each revision is like a snapshot of the application**. 
	- *Every time a change to the application is made like upgrade of an image or change of replicas a new revision is created*.
- We can find the helm charts in a public repository. We can use them to deploy applications on our cluster.
- To keep track of what it did on our cluster it saves it as metadata.
	- *Helm saves this metadata directly in our k8s cluster as k8s secrets*.

- Templating in charts
	- *`values.yaml` is the file where configurable values are stored* and most of the times it is the only file that we will need to modify to customise the deployment of the application.
	- ![[attachments/Pasted image 20221004000946.png]]

- Charts are more complicated for complex applications like a wordpress application.
- `helm install <release-name> <chart-name>` 
	- *We have release names with charts because we can have multiple releases with the same chart*.
	- These 2 separate releases can be tracked separately even though they are from the same chart.
	- ![[attachments/Pasted image 20221004001326.png]]