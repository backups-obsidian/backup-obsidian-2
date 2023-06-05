---
created: 2023-06-02 11:59
updated: 2023-06-05 13:17
---
---
**Links**: [[111 KodeCloud Index]]

---
## Introduction
- It is *an operator* and has its own API.
- Suppose you have 3 different environments (dev, stage and prod).
	- Now for each of these environments you would want a different number of replicas for the application.
	- The easiest way of doing this would be to create individual yaml files for each environment.
	- *This solution is NOT scalable*.
- **We want a way to reuse the k8s configs and only modify what needs to modified on a per environment basis**.
- *Kustomize has 2 main components: `base` and `overlay`*.
- *`base` + `overlay` is combined to form the final manifest*.
- `base` component will be same for all the envrionments.
- `base` component can also have default values which can be overridden by `overlay`.
	- Diagram:
		- ![[attachments/Pasted image 20230602120757.png]]
- Directory Structure:
	- ![[attachments/Pasted image 20230602120857.png]]
- *Kustomize comes built-in with kubectl so no other packages need to be installed*.
	- May still want to install the kustomize cli to get the latest version. 
	- Kubectl doesn't come with the latest version.
- It *DOES NOT require learning how to use any complex & hard to read templating systems(like helm)*.
- *Every artifact the Kustomize uses is plain YAML* and can be validated and processed as such.

## Basics
- Kustomize **looks for a kustomization file** which contains:
	- *List of all the Kubernetes manifests kustomize should manage*.
	- *All of the customizations that should be applied*.
- The *kustomize build* command combines all the manifests and applies the defined transformations
	- The kustomize build command does not apply/deploy the Kubernetes resources to a cluster.
	- The output needs to redirected to the kubectl apply command.
- Applying resources using kustomize:
	- `kustomize build k8s/ | kubectl apply -f -`
	- `kubectl apply -k k8s/`
- Deleting resources using kustomize:
	- `kustomize build k8s/ | kubectl delete -f -`
	- `kubectl delete -k k8s/`

### Managing Directories
- Suppose we have a lot of folders containing resource manifests and we want kustomize to manage all the resources.
	- The kustomize file would look something like this:
		- ![[attachments/Pasted image 20230602124016.png]]
- It will start getting messy if the number of folders or resources increase.
	- *Instead we can include a `kustomization.yaml` file in each of the directories and just specify the directories in the base `kustomization.yaml` file*.
		- ![[attachments/Pasted image 20230602124147.png]]

- Sample kustomization file: 
```yaml
# Not necessary but good to provide the apiVersion and kind
apiVersion: kustomize.config.k8s.io/vlbeta1
kind: Kustomization

resources:
	# NOTE: that the files are relative to this kustomization file
	# So if we want to include files inside a directory it will be something like directory/yaml-file.yaml
	- yaml-files.yaml

# transformations or patches to be listed below
```

### Transformers 
- `commonLabel`: adds a label to all Kubernetes resources
- `namePrefix/Suffix`: adds a common prefix-suffix to all resource names
- `Namespace`: adds a common namespace to all resources 
- `commonAnnotations`: adds an annotation to all resources
- Image transformer:
	- Modifying the image:
		- ![[attachments/Pasted image 20230602140428.png]]
	- Modifying the tage:
		- ![[attachments/Pasted image 20230602140414.png]]
	- Modifying the image & tag:
		- ![[attachments/Pasted image 20230602140527.png]]

### Patches
- Kustomize patches provide another method to modifying Kubernetes configs
- *Unlike common transformers, patches provide a **more "surgical" approach** to targeting one or more specific sections in a Kubernetes resource*.
- There are *2 different ways of patching*.
	- JSON 6902
	- *Strategic merge patch*
- Difference between **JSON 6902 patch vs strategic merge patch**:
	- ![[attachments/Pasted image 20230605121354.png]]
 
> [!tip] Use strategic merge patch since it is similar to k8s config files.

- *Using a separate file for a strategic merge patch*
	- ![[attachments/Pasted image 20230605121635.png]]
- **In strategic merge patch we only specify the components that we need to change**.
	- The trick is to copy the full yaml file and delete the things that need not be changed.

#### Strategic Merge Patch Examples
- *Replacing a key in dictionary*:
	- ![[attachments/Pasted image 20230605122051.png]]
- *Adding a dictionary*:
	- ![[attachments/Pasted image 20230605122335.png]]
- *Removing a dictionary*:
	- ![[attachments/Pasted image 20230605122429.png]]
- *Replace list*:
	- ![[attachments/Pasted image 20230605122718.png]]
- *Add list*:
	- ![[attachments/Pasted image 20230605122818.png]]
- *Deleting a list*:
	- ![[attachments/Pasted image 20230605122911.png]]

### Overlays
- *Folder structure in overlays*:
	- ![[attachments/Pasted image 20230605123409.png]]
- **Overlays uses patches to modify the base configuration files**.
	- ![[attachments/Pasted image 20230605123718.png]]
- **In overlays we can also define new configs that weren't defined in the base folder**.
	- ![[attachments/Pasted image 20230605123621.png]]
- The examples above were using the JSON 6902 patch but we can easily use the strategic merge patch strategy.
- There is a lot of flexibility in the folder structure when we use kustomize.
	- We need not jam everything in the base directory.
	- We can use sub folders.
	- The sub directories in the overlays NEED NOT match with the subdirectories of the base directory.
	- Diagram:
		- ![[attachments/Pasted image 20230605123918.png]]

### Components
- Components provide the ability to define *reusable pieces of configuration logic(resources + patches) that can be included in multiple overlays*.
- Components are useful in situations where applications support multiple optional features that need to be **enabled only in a subset of overlays**.
- Explanation of the scenario with a diagram:
	- ![[attachments/Pasted image 20230605124422.png]]
	- We CAN'T have the caching feature in the base since dev will also get it.
	- We can copy and paste it to premium and self hosted but it is NOT advised.
- Solution diagram:
	- ![[attachments/Pasted image 20230605124719.png]]
- In case of components the **`kind: Component**`**.
- **Defining components**:
	- ![[attachments/Pasted image 20230605125330.png]]
- **Using components**:
	- ![[attachments/Pasted image 20230605125427.png]]

## Secret/ConfigMap Generator
> [!question]- Why do we need Secret/ConfigMap generator?
> - We need it since *if we change the ConfigMap or Secret then the deployments depending on it are NOT restarted*.
> - We have to manually do `k rollout restart deployment <deployment-name>` for the new ConfigMap or Secret to be picked up.

- How generators work:
	- When we use generators and define a ConfigMap/Secret it appends some characters to the name which makes it unique.
	- We reference the original name and kustomize adds the unique characters at the end of the name.
	- Now when we change the ConfigMap/Secret kustomize appends some unique characters at the end of the name and updates the deployment too.
	- Now since the deployment is updated it is restarted.
	- Diagram:
		- ![[attachments/Pasted image 20230605130231.png]]
- *Using generators - Key value pairs*:
	- ![[attachments/Pasted image 20230605130628.png]]
- *Using generators - files* 
	- ![[attachments/Pasted image 20230605130835.png]]

> [!note] The files should be in the same directory as the `kustomization.yml` where the generator was defined.

- Everything remains the same when we use the Secret generator only difference being that the *values we provide are automatically base64 encoded*.
	- ![[attachments/Pasted image 20230605131046.png]]
	- ![[attachments/Pasted image 20230605131116.png]]
