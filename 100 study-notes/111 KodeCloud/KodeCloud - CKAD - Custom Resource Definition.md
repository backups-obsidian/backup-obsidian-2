---
created: 2022-09-15 21:09
updated: 2023-05-11 16:51
---
---
**Links**: [[111 KodeCloud Index]]

---
## Custom Resource Definition (CRD)
- Some times we will run into *situations where the k8s resources* (pod, deployment, service etc) *are too limiting* or not precisely what we need.
	- For this purpose k8s supports **custom resource definitions (CRDs)**.
	- **We use CRD to create any kind of resource in k8s**.
- Each resource in k8s (like pod, deployment, etc) is an endpoint on the k8s API.
	- *Custom resources are a way of extending that API*.
- Once the custom resource is installed in our cluster we can create an object from it.

- For every resource we have controller. 
	- So when we create a resource it is stored in etcd.
	- Controller continuously watches the status of these objects to maintain the state as expected.

> [!note] Once we create a CRD we can create the custom resource.

- **Creating a CRD only allows us to create custom resources and store them on etcd**.
	- Since we DON'T have a controller yet.
	- A *custom resource WITHOUT a controller is just data sitting in etcd and does not do anything*.

> [!important]+ So there are 2 steps for creating any custom resource in k8s
> - Defining a CRD
> - Writing a controller

- *We write a custom controller using the go programming language*.
	- Once we build the controller we compile it.
	- We specify the kubeconfig file that the controller can use to authenticate to the k8s api.
	- We may choose to package the custom controller as a docker image and run it as a pod or deployment.

- Many *tools that we install* on your K8S cluster will include the *creation of some CRD objects*. 
	- This is to enable them to provide functionality that is not provided in vanilla K8S.
	- For example, if we have the monitoring tool Prometheus installed on our cluster, it will include the installation of `prometheusrules.monitoring.coreos.com` CRDs.

> [!note] CRDs are listed in the format `<object>.<group>`

### Example yaml files for creating a CRD
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: switches.datacenter.com  # name must match the spec fields below, and be in the form: <plural>.<group>
spec:
  group: datacenter.com  # group name to use for REST API: /apis/<group>/<version>
  versions:
    - name: v1 
      served: true # Each version can be enabled/disabled by Served flag.
      storage: true # One and only one version must be marked as the storage version.
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                dataCenter:
                  type: string
                rack:
                  type: integer
                type:
                  type: string
  scope: Cluster # either Namespaced or Cluster
  names:
    plural: switches # plural name to be used in the URL: /apis/<group>/<version>/<plural>
    singular: switch # singular name to be used as an alias on the CLI and for display
    kind: Switch # kind is normally the CamelCased singular type. Your resource manifests use this.
    shortNames: # shortNames allow shorter string to match your resource on the CLI
    - sw
```

- Now we can use: `kubectl get switches`
- Creating a switch object
```yaml
apiVersion: datacenter.com/v1
kind: Switch 
metadata:
  name: switch1
spec:
  dataCenter: uksouth
  rack: 280
  type: physical
```
