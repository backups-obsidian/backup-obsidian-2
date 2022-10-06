---
created: 2022-10-06 11:49
updated: 2022-10-06 14:45
---
---
**Links**: [[111 KodeCloud Index]]

---
## Writing a helm chart
- Helm charts are like installers, they can do much more than installing applications.
	- For example we can have hooks to backup the data whenever we upgrade the chart.
	- ![[attachments/Pasted image 20221006115232.png]]

- **Helm chart is a directory with a specific structure**.
	- When writing a helm chart our goal is to create a directory structure first.
	- We can use `helm create <chart-name>` to create a skeleton structure.

- Once we place the relevant files in the `templates` directory our helm chart should be ready to go.
	- We can now install the helm chart using the helm install command.
	- The only problem is that these charts have static values in them.
- Every time you install a helm chart the name of the deployment, service etc should be different otherwise it will give an error.
	- ![[attachments/Pasted image 20221006120008.png]]
- We can use helm's templating language to make sure that the name of the deployment is named after release name (`{{ .Release.Name }}`.
	- ![[attachments/Pasted image 20221006120111.png]]
	- Anything that starts with Values refers to the `values.yaml` file.

> [!note]- We use go templating syntax in helm.

- Some other objects. *Notice the Case*
	- ![[attachments/Pasted image 20221006120527.png]]
	- The first 3 are built in objects and must follow the capital letter naming convention.
	- Values start with a small case. *Everything under the values is user defined* and users are free to follow whatever naming convention they like.

- Using dictionary in `values.yaml`
	- ![[attachments/Pasted image 20221006121029.png]]

- Verifying helm charts without installing them:
	- `helm lint <path-to-chart-directory>`: catch linting errors like spacing and naming.
	- `helm template <release-name> <path-to-chart-directory>`: renders the chart locally. 
		- If we don't pass in the release name the default release name of *RELEASE-NAME* will be used.
	- `helm template <path> --debug`: for debugging errors.
	- `helm install <release-name> <path> --dry-run`: Catches if k8s manifest format is incorrect.

- **Summary**:
	- ![[attachments/Pasted image 20221006121239.png]]

## Functions
- We need some default values to fall back on incase the user's don't provide anything in their `values.yaml` file.
- Functions in helm help transform data from one format to another.
- *Some string functions*
	- ![[attachments/Pasted image 20221006144137.png]]
- We have different kind of functions 
- **Default value function**
	- ![[attachments/Pasted image 20221006144435.png]]
	- *Values always have to be in quotes*. If they are not in quotes then they are considered as variables