---
created: 2022-09-15 21:09
updated: 2022-10-10 12:35
---
---
**Links**: [[111 KodeCloud Index]]

---
## ApiVersions
![[attachments/Pasted image 20221010104405.png]]

- By default **Alpha APIs are NOT enabled**. For example
	- ![[attachments/Pasted image 20221010104559.png]]
	- ![[attachments/Pasted image 20221010104701.png]]
	- This will give an error
- Alpha APIs can contain bugs and may be dropped later.

- **API Groups in Beta stage are enabled by default**.
- Difference between Alpha, Beta and GA versions
	- ![[attachments/Pasted image 20221010105037.png]]

- An API group can support multiple versions at the same time but there can be only one preferred/storage version.
	- ![[attachments/Pasted image 20221010105217.png]]
	- When we run the `k get deploy` command then the *preferred version is queried*. 

- Preferred and storage version can be different.

- Getting the preferred version of an API group: For example authorization.k8s.io
	- `kubectl proxy 8001 
	- `curl localhost:8001/apis/authorization.k8s.io`

## ApiDeprecation
- API elements may only be removed by incrementing the version of the API group.
	- ![[attachments/Pasted image 20221010105807.png]]

## Kubectl convert
> [!caution]+ When old API versions are deprecated we need to change our old manifest files. 
> - This can be done using `kubectl convert -f ‹old-file> --output-version ‹new-api>`
> - It doesn't change the file, just spits the output to stdout. If we need it as a file then we need to direct the output to a file.
> ![[attachments/Pasted image 20221010114159.png]]

- The `kubectl convert` command may not be available on our system as it is a separate plugin.