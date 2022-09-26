---
created: 2022-09-26 16:37
updated: 2022-09-26 16:57
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
- While labels and selectors are used to group and select objects **annotations are used to record other details for informatory purpose**.