---
created: 2022-09-15 21:09
updated: 2023-06-30 20:50
---
---
**Links**: [[111 KodeCloud Index]]

---
## What are Helm Charts
- **Helm** is also known as a **package manager for k8s**.
- We *use a single command to install our application even if it needs hundreds of objects*.
- We can customise the settings we want for our app by specifying desired values at install time (`values.yaml`).
	- Instead of editing multiple values in multiple yaml files we have a *single location* where we can declare every custom setting.

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
	- **`values.yaml` is the file where configurable values are stored** and most of the times it is the only file that we will need to modify to customise the deployment of the application.
	- ![[attachments/Pasted image 20221004000946.png]]

- Charts are more complicated for complex applications like a wordpress application.
- `helm install <release-name> <chart-name>` 
	- *We have **release names** with charts because we can have multiple releases with the same chart*.
	- These 2 separate releases can be tracked separately even though they are from the same chart.
	- ![[attachments/Pasted image 20221004001326.png]]

- Upgrading the application: `helm upgrade <release-name>`
- Roll back to the previous revision using: `helm rollback <release-name>`
- Uninstalling the app: `helm uninstall <release-name>`
	- *It keeps track of all the objects used by each app so it knows what to remove*.
	- We don't need to remember each object that belongs to our app anymore.

- Apart from `values.yaml` file every chart has a `chart.yaml` file. 
	- It contains information about the chart itself.
	- *apiVersion for helm 2 is v1 and for helm 3 is v2*. If a chart does not have this value set then it means that it was built for helm 2.
	- There are 2 types of chart: application and library.
	- In the dependencies section we can add other charts as dependencies.
	- ![[attachments/Pasted image 20221004101549.png]]

- **Helm chart structure**:
	- ![[attachments/Pasted image 20221004101630.png]]

- We can find help on any command or subcommand of helm using `--help`
	- `helm --help`: to get a list of commands that we can use
	- `helm <command> --help`
	- `helm <command> <sub-command> --help`

- We can search for charts in the artifact hub using the helm cli:
	- `helm search hub <application-name>`
- If you have added a repo using `helm repo <name> <url>` then you can search in it using
	- `helm search repo <application-name>`

- We can list all existing releases using `helm list`
- List all the repositories present: `helm repo list`
- Refreshing the repos: `helm repo update`
	- It is just like `sudo apt update`
	- This is needed since the information helm has about the repositories is stored locally and it gets updated over time.

- We can install a specific version of helm chart using: 
	- `helm install <release-name> <chart-name> --version 3.3`
	- *This is the chart version and NOT the application version*.

## Customising chart parameters
- `values.yaml` file
	- ![[attachments/Pasted image 20221005225341.png]]
- Using set option to customise the value
	- `helm install --set wordpressBlogName-"Helm Tutorials" my-release bitnami/wordpress`
	- This can be used multiple times to pass multiple parameters to the command line.
	- These **override the values set in `values.yaml` file**.
	- ![[attachments/Pasted image 20221005225813.png]]
- If there are too many of these values we can move them to *our own custom values yaml file*.
	- This will now override the values in the default `value.yaml` file.
	- `helm install --values custom-values.yaml my-release bitnami/wordpress`

> [!question]- What if we want to change the values in `values.yaml` without using a custom values file or `--set` option
> We will break it into 2 commands.
> - We will first pull the chart using `helm pull bitnami/wordpress` in an archive or compressed form.
> - We can uncompress it using helm while pulling using the `helm pull --untar bitnami/wordpress`
> - We then run the charts `helm install <release-name> <extracted-folder-name>`
> ![[attachments/Pasted image 20221005234808.png]]

## Lifecycle management
- More details about a particular release:
	- `helm history <release-name>`
- Going back to a previous release
	- `helm rollback <release-name> <revision-number>`
	- Here revision number is the number we want to revert back to.

> [!note]- Suppose we do a rollback from v2 to v1, technically helm does not go back to v1, instead it creates a new revision (v3) with a similar configuration to v1.

- Although rollbacks are very similar to backup restore feature, it does not cover file or directory data that may be created by our application.
	- **So for things that use persistent volumes, rollback won't restore the data**. 
	- This is done using chart hooks

## References
- [Helm and Helm Charts Explained - Helm Tutorial for Beginners - YouTube](https://www.youtube.com/watch?v=w51lDVuRWuk)
- [How to Use Helm to deploy Helm Charts in Kubernetes: Practical Tutorial - YouTube](https://www.youtube.com/watch?v=gg-GuHs8Nsk)