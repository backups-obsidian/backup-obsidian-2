---
created: 2022-09-23 17:15
updated: 2022-09-23 17:28
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
	   command:["sleep2.0"]
	   args: ["10"]
```

> [!note]- In the above example `command` is equivalent to the `ENTRYPOINT` instruction in the `Dockerfile` and the `args` is equivalent to the `CMD` instruction
> ![[attachments/Pasted image 20220923172657.png]]

