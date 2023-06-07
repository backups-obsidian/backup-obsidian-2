---
created: 2023-06-06 10:49
updated: 2023-06-07 12:36
---
---
**Links**: [[111 KodeCloud Index]]

---
## Introduction - GitOps
- GitOps process diagram:
	- ![[attachments/Pasted image 20230606122002.png]]
- **In GitOps pipeline we have 2 repositories**.
	- One for the application code and other for the infrastructure.
- **Difference between GitOps and DevOps**:
	- ![[attachments/Pasted image 20230606122710.png]]

> [!note] In *GitOps changes were pulled by the operator* and in *DevOps changes were pushed to the clutser*.

- **Difference between push and pull based approach**:
	- ![[attachments/Pasted image 20230606123206.png]]

> [!note] The main drawback of a push based approach is that cluster config is embedded in the CI system.

- A **single GitOps operator can deploy applications to multiple clusters** without the need for it to be installed or setup on all the clusters.
	- ![[attachments/Pasted image 20230606123519.png]]
- Some Benefits of GitOps:
	- It is vendor neutral.
	- Git is the source of truth.
	- Helps in eliminating configuration drift.
	- We have revisions of the infrastructure with git history.

## ArgoCD
- Argo CD is a *declarative, GitOps continuous delivery tool* for *Kubernetes resources* defined in a Git repository.
	- Continuously monitors running applications and comparing their live state to the desired state
	- It reports the deviations and provides visualizations to help developers manually or automatically sync the live state with the desired state.
- *Terminologies*:
	- **Application**: *It is a custom resource definition which represents a deployed application in k8s*.
		- It has *2 main components*
			- *Source repository URL*
			- *Destination cluster*
		- Application source type: The application can have different source types. E.g. Helm, Kustomize or Ksonnet
		- Diagram:
			- ![[attachments/Pasted image 20230607102502.png]]
	- **Project**: *Provide a logical grouping of applications*, which is useful when Argo CD is used by multiple teams.
		- *By default a `default` project is created for us in ArgoCD which is very permissive*.
			- ![[attachments/Pasted image 20230607104627.png]]
	- Target state: The desired state of an application, as represented by files in a Git repository.
	- Live state: The live state of that application. 
		- What pods, configmap, secrets, etc are created/deployed in a Kubernetes cluster.
	- **Sync status**: Whether or not the live state matches the target state. 
		- *Is the deployed application the same as Git says it should be*?
		- In case of rollbacks the sync status will always be out of sync.
	- *Sync*: The process of making an application move to its target state. 
		- E.g. by *applying changes to a Kubernetes cluster*.
	- **Refresh**: Compare the latest code in Git with the live state. 
		- Figure out what is different.
	- **Health**: The health of the application, is it running correctly? Can it serve requests?
		- We can create custom health checks.
			- For example this is particularly useful since config maps have no predefined health check.
		- Diagram:
			- ![[attachments/Pasted image 20230607112802.png]]
- **ArgoCD Architecture**:
	- ![[attachments/Pasted image 20230606125000.png]]

> [!note] Both the UI and the CLI talks to the ArgoCD Rest API.

- *ArgoCD installation types*:
	- ![[attachments/Pasted image 20230606125301.png]]
- If we *want to use ArgoCD cli for accessing its API we have to install it separately*.
- *ArgoCD has a reconciliation loop which by default runs every 3 minutes to poll the data from the source git server*.
	- This can be changed by adding information in the argocd configmap and then restarting the ArgoCD deployment.
- Diagram for understanding the difference between sync and refresh:
	- ![[attachments/Pasted image 20230607111430.png]]
- *We can also use a webhook based approach if we want to remove the polling delay*.

### Sync Strategies
- When ArgoCD detects a new version of our application in git it either performs a manual sync or an automated sync.
	- If set to *automatic*, *Argo CD will apply the changes then update or create new resources in the target Kubernetes cluster*.
	- If set to *manual*, *Argo CD WON'T make any changes* until and unless a user clicks the sync button on the UI.
- **Auto-pruning** feature describes what happens when files are deleted or removed from Git.
	- **If this feature disabled then argocd WON'T delete anything from the cluster**.
- Enabling the **self heal feature** will ensure that any manual edits made to the cluster will be reverted back to the state described in Argo CD.
- *By default syncing is set to manual and auto pruning and self healing is disabled*.

> [!note]- Best configuration: manual sync with auto pruning and self healing enabled.
> Can I only enable auto pruning and self healing in argocd without enabling automatic sync?
> ---
> - To enable auto pruning, you can set the `--auto-prune` flag to `true` when starting the Argo CD server: `argocd-server --auto-prune=true`
> - To enable self-healing, you can set the `--self-heal` flag to `true` when starting the Argo CD server: `argocd-server --self-heal=true`
> - We can enable auto pruning and self-healing in Argo CD without enabling automatic sync by setting the respective flags as mentioned above while leaving the sync behaviour unchanged.

### Declarative Setup
- We can create ArgoCD applications using UI or ArgoCD cli.
- **We can also use k8s like manifests to create applications ArgoCD**.
	- ![[attachments/Pasted image 20230607115215.png]]
- **App of apps pattern**:
	- ![[attachments/Pasted image 20230607122206.png]]
- App of apps manages other ArgoCD applications.

### Multi Cluster Deployment
- Argo CD can be used to manage deployments in multiple cluster.
- *Argo CD gets the cluster credentials from the kubeconfig file*.
- **So we need to update out kubeconfig file to have the details of the external server**.
- Once we have the cluster credentials in the kubeconfig file we need to add the cluster to argocd
	- `argocd cluster add <context-name>`
- We can list the clusters using: `argocd cluster list`
- *Credentials to external clusters are stored as secrets within the Argo CD namespace*.
- **Diagram**:
	- ![[attachments/Pasted image 20230607124009.png]]

