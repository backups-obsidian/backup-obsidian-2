---
created: 2022-09-15 21:09
updated: 2023-05-22 20:38
---
---
**Links**: [[111 KodeCloud Index]]

---
## Operators
- Operators are pieces of software that **ease the operational complexity of running another piece of software**.
	- It is a method of *packaging, deploying, and managing a Kubernetes application*.
- *Operators replace the human operator with a software operator*.
	- For example all the manual tasks that the DevOps team would do is packed into a software.
	- For a *stateful application like postgres or prometheus* some tasks would be: 
		- How to deploy the app
		- How to create a cluster of replicas
		- How to recover from failures
		- How to *create backups*
	- A Kubernetes operator continues to monitor its application as it runs, and can *back up data*, recover from failures, and *upgrade the application over time, automatically*.
	- Anything that is a part of an application lifecycle can be automated by the operator.
- An operator **packages CRD and its controller together**.
	- In order to do these things, the Operator uses **Custom Resources (CR)** that define the desired configuration and state of a specific application through **Custom Resource Definitions (CRD)**.
- Operators are mainly used for managing **stateful applications** and installing third party software like prometheus, grafana etc.
- Operators builds upon the basic Kubernetes resource (using CRD) and controller concepts, but *includes domain or application-specific knowledge to automate the entire life cycle of the software it manages*.
- Operators for a lot of software like elastic search, postgres, mysql have already been created for us.
	- [**Artifact HUB**](https://artifacthub.io/packages/search) – where we find both operators and helm charts (CNCF project)
	- [**Operator Hub**](https://operatorhub.io/) – dedicated exclusively to Operators (Redhat project)
- Some popular k8s operators are: Prometheus, Grafana, Elastic Cloud on Kubernetes, RBAC Manager, Istio, ArgoCD.
- If we want to *create our own operators* then we have to use the *operator SDK or kubebuilder*.
- We can build *custom operators* using *ansible playbooks*, *helm charts* or *go*.
	- There are **different levels of capability** that can be achieved using these theres.
		 - ![[attachments/Pasted image 20230512115855.png]]

> [!note] Operators are best built by those that are *experts in the business logic* of installing, running, and upgrading a particular application.

- Operators are generally **deployed using helm charts**.

> [!question]- Why should I use Operators when I can just use Helm charts for deploying applications?
> - Helm and Operators are to be used in different types of use cases.
> - With **Helm** you can *package your applications*. Just like an RPM package on Linux ! So you can continue to use Helm when **deploying a simple application and when few customizations are required**.
> - We need **operators** to perform actions described previously such as *deploy stateful applications*. It will then manage a set of instances, *reconciling its state according to the mentioned specs of its CR*.


## References
- [What is a Kubernetes operator? (redhat.com)](https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-operator)
- [Kubernetes Operators: what are they? Some examples | Cloud Native Computing Foundation (cncf.io)](https://www.cncf.io/blog/2022/06/15/kubernetes-operators-what-are-they-some-examples/)
- [What is an Operator? Kubernetes & OpenShift Operators Explained. - YouTube](https://www.youtube.com/watch?v=MfDpQru0-ok)
- [Should We Run Databases In Kubernetes? CloudNativePG PostgreSQL - YouTube](https://www.youtube.com/watch?v=Ny9RxM6H6Hg)