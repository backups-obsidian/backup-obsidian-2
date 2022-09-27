---
created: 2022-09-26 16:37
updated: 2022-09-26 20:01
---
---
**Links**: [[111 KodeCloud Index]]

---
- *Labels and Selectors* are a *standard method to group things together* and filter them according to your needs.
	- **Labels are properties attached to each item**.
		- ![[attachments/Pasted image 20220926163615.png]]
	- **Selectors help us filter these items**.
		- ![[attachments/Pasted image 20220926163647.png]]

- Over a period of time we may have a ton of objects in k8s. We will need a way to group them together.
	- ![[attachments/Pasted image 20220926163911.png]]
- Sample pod definition file specifying labels
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: simple-webapp
	labels:
		app: Appl
		function: Front-end
spec:
	containers:
	- name: simple-webapp
	  image: simple-webapp
	  ports:
	  - containerPort: 8080
```

- We can have as many labels we want.
- Selecting pods with specific labels:
	- `k get pods --selector app=App1`
	- ![[attachments/Pasted image 20220926164548.png]]
- `k get all -o wide --selector env=Prod`
- Specifying multiple selectors:
	- `k get all --selector env=prod,bu=finance,tier=frontend`

## Annotations
- While labels and selectors are used to group and select objects whereas annotations are used to attach arbitrary **non-identifying metadata** (k8s doesn't care about this data) to objects.
	- *Clients such as tools and libraries can retrieve this metadata*.

> [!important]- You can use *either labels or annotations to attach metadata to Kubernetes objects*. Labels can be used to select objects and to find collections of objects that satisfy certain conditions. In contrast, **annotations are not used to identify and select objects**.

- Annotations are also key-value pairs (similar to labels) that can be used to *store the **unstructured information** pertaining to the Kubernetes objects*.
	- Annotations can be used to add timestamps, commit SHA, issue tracker links, or names/information about users who are responsible for specific objects in an organisation.
	- Annotations can also be used to *add information about client libraries or tools*.

> [!note]- Labels are for k8s while annotations are for humans.
> - Labels have some constraints, annotations have no constraints.

> [!caution]- While annotations do not inherently imply semantics to the Kubernetes core, it is still *possible for them to affect operation in some cases*. 
> - A good example of this is with the NGINX Ingress controller (along with other ones). 
> - The **NGINX Ingress controller allows you to add Kubernetes annotations onto your ingress objects to affect their behaviour**. 
> - Most of these map cleanly to the configuration options available in NGINX, and as such, it is a nice way to allow mapping NGINX specific concepts onto your Kubernetes resources.
> - The *NGINX Ingress controller is then able to read these annotations and apply them as needed*. 