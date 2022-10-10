---
created: 2022-09-15 21:09
updated: 2022-10-10 10:43
---
---
**Links**: [[111 KodeCloud Index]]

---
## Admission Controllers
- The *rules we can create in RBAC is at the k8s api level*.
- What if we want to do more?
	- Like reviewing the configuration file and saying that only permit images from certain registry.
	- Or enforce that we should never use the latest tag for any images.
	- Or do not permit run as root user. 
	- Or enforce that metadata always contains labels.
- These things can't be achieved using authorisation and this is where admission controllers are used.
	- Admission Controllers help us implement better security measures to enforce how a cluster is used. 
	- ![[attachments/Pasted image 20221008205407.png]]
- Example: NamespaceExists Admission Controller
	- Creating a pod in a namespace that does not exist
	- We would get an error.
	- This is a built in admission controller
	- This is enabled by default
	- ![[attachments/Pasted image 20221008213606.png]]

- We have another namespace admission controller called NamespaceAutoProvision
	- This is not enabled by default. 
	- This will automatically create the namespace if it does not exist.

- View the list of admission controllers *enabled by default*
	- `kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver | grep enable-admission-plugins`
- To add an admission controller we update the `--enable-admission-plugins` flag.
- To disable an admission controller we can update the `disable-admission-plugins` flag.

> [!important]- Using admission controllers we can not only validate or reject requests but also change the requests by performing actions in the backend.

- There are 2 types of admission controller:
	- Mutating Admission Controller: It can change/mutate the object before it is created
		- Example: DefaultStorageClass 
		- DefaultStorageClass: If we don't provide a storage class in the PVC then it will modify our request to add the default storage class as the storage class. 
		- When the PVC is created and we inspect it we see that the storage class is set to default by the admission controller.
	- Validating Admission Controller: It just validates the request and rejects it if it is not valid.
		- Example: NamespaceExists

> [!note]- Mutating admission controllers are invoked first followed by validating admission controller.
> This is done so that any changes made the object by the mutating admission controller can then be validated by the validating admission controller.
> For example NamespaceAutoProvision runs before NamespaceExists admission controller.

- *To use external admission controllers k8s provides 2 admission controllers*:
	- MutatingAdmissionWebhook
	- ValidatingAdmissionWebhook

- We point these webhooks to the server running the admission controllers.
	- ![[attachments/Pasted image 20221010103814.png]]

- INCOMPLETE