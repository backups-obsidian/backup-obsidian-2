---
created: 2022-09-23 17:15
updated: 2022-09-23 18:22
---
---
**Links**: [[111 KodeCloud Index]]

---
## Commands & Arguments
- In case of `ENTRYPOINT` in docker whatever command you specify will get appended to `ENTRYPOINT` command.
- In case of `CMD` the command line instruction will get replaced entirely whereas in case of `ENTRYPOINT` the command line parameters will get appended.
	- ![[attachments/Pasted image 20220923173111.png]]

- Pod definition with `ENTRYPOINT` and `CMD`
```yaml
apiVersion: vl
kind: Pod
metadata:
	name: ubuntu-sleeper-pod
spec:
	containers:
	 - name ubuntu-sleeper
	   image: ubuntu-sleeper
	   command:
		- "sleep2.0"
	   args: 
	    - "10"
```

> [!note]- In the above example `command` is equivalent to the `ENTRYPOINT` instruction in the `Dockerfile` and the `args` is equivalent to the `CMD` instruction
> ![[attachments/Pasted image 20220923172657.png]]

- The `commands` and `args` must be in quotes.

- In general I have only seen the use of command and not args in a definition file.
```yaml
apiVersion: vl
kind: Pod
metadata:
	name: ubuntu-sleeper-pod
spec:
	containers:
	 - name: ubuntu-sleeper
	   image: ubuntu-sleeper
	   command:
		- "sleep2.0"
	    - "10"
```

- Questions: Find the command being run assuming the pod is created from the image of Dockerfile.
- ![[attachments/Pasted image 20220923174801.png]]
	- `python app.py --color pink`
- ![[attachments/Pasted image 20220923174813.png]]
	- `--color green`

> [!caution]- Basically `command` overrides the `entrypoint` of `Dockerfile`

- *Commands is an array* so they can also be given in the following way
```yaml
apiVersion: vl
kind: Pod
metadata:
	name: ubuntu-sleeper-pod
spec:
	containers:
	 - name: ubuntu-sleeper
	   image: ubuntu-sleeper
	   command: ["sleep", "10"]
```