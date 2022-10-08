---
created: 2022-10-06 11:49
updated: 2022-10-08 00:46
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
		- `{{ .Values.image.tag | default .Chart.AppVersion }}`: `.Chart.AppVersion` is not in quotes hence it is a variable here.
- **Pipelines are another way of using helm functions** and are much more common way of using functions
	- Using pipelines we can pipe functions
	- `{{ .Values.image.repository | upper | quote }}` -> `image: "NGINX"`

- To check if you are getting the right output after using functions we can use the `helm template <path>` command to generate the full template.

## Conditionals
- Specify the labels only if they are present:
	- ![[attachments/Pasted image 20221006150949.png]]
	- If the org labels value is not set in the `values.yaml` file then it won't be available in the `service.yaml` file also.
- **When the template file is converted to a manifest file everything between the curly braces is removed** this results in empty spaces in the manifest file.
	- ![[attachments/Pasted image 20221006151639.png]]
- We can remove those extra lines using the following syntax
	- ![[attachments/Pasted image 20221006152247.png]]
- `else if`:
	- ![[attachments/Pasted image 20221006152530.png]]

- We can create files based on arguments in the `values.yaml` file.
	- ![[attachments/Pasted image 20221006152729.png]]
	- Notice that the if statement wraps the whole file
	- We can also use `with` in this example
	- ![[attachments/Pasted image 20221006161555.png]]

- Indentation is important when writing values under conditional
	- This is correct:
		- ![[attachments/Pasted image 20221006160240.png]]
	- This is wrong:
		- ![[attachments/Pasted image 20221006160341.png]]
	- This can be verified using `helm template <path> --debug`

## Setting scope With
- `.` is a reference to the **root scope**.
- A sample scope hierarchy:
	- ![[attachments/Pasted image 20221006153000.png]]
	
> [!note]- If we don't set the scope then by default current scope is set to **root** for the file.
> We see that there is duplication. We can avoid this by setting the scope.

- We can set a scope using the `with` block
	- ![[attachments/Pasted image 20221006153255.png]]
	- Now within the `with` block the scope is set to `.Values.app`
	- Every `with` block has and `end` block

- Nesting scopes
	- ![[attachments/Pasted image 20221006153508.png]]

> [!caution]+ 
> ![[attachments/Pasted image 20221006153655.png]]
> This will give an error since `.Release.Name` is inside another scope.
> To reference the root scope inside the `with` block we have to use `$`
> `$.Release.Name`

## Loops 
- We loop using `range`
- Just like the `with` block the `range` block sets the scope each time it iterates through the list.
	- *So `{{ . }}` will refer to the value in the list*.
	- ![[attachments/Pasted image 20221006154510.png]]
	- We can consider `.` as any other object and pipe it through functions

## Named templates
- If there are somethings we repeat a lot we can move them to a named template file.
	- ![[attachments/Pasted image 20221006201134.png]]
- It starts with `_` and ends with a `.tpl` extension.
- We use a `.` at the end to transfer the scope from the template file to the helper file.
- Be wary of the spacing.
- **Indentation issue and fix**
	- Templates are actions and we cannot pass it to functions. 
	- We use the `include` keyword instead of the `template` keyword to use it as an function instead of an action.
	- ![[attachments/Pasted image 20221006201745.png]]

## Chart Hooks
- *Automatically backing up the database* when we do a helm upgrade. 
	- This is an *extra action* which is *implemented using a hook*.
- `pre-upgrade` hook example: backing up a database
	- ![[attachments/Pasted image 20221006202140.png]]
- `post-upgrade` hook example: sending an email status
- Jobs are used to run the hooks.
- **We use annotations to tell helm that this job is a part of a hook and is not a regular template file**.
	- ![[attachments/Pasted image 20221006202652.png]]
- We *can have multiple pre upgrade hooks*.
	- **We use weights to define in which order these hooks are to be executed**. The weights can be -ve or +ve numbers.
	- During chart installation phase helm sorts these in **ascending order**.
	- We can set the same weight for multiple hooks
	- We can set a *hook deletion policy* to clean up the resource (the pod) that were used for running the jobs.
		- `before-hook-creation` deletes the previous resource before a new hook is launched
	- ![[attachments/Pasted image 20221006203111.png]]
