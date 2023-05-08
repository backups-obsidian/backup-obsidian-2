---
created: 2022-09-15 21:09
updated: 2023-05-05 17:08
---
---
**Links**: [[111 KodeCloud Index]]

---
## Network Policies
- Ingress and Egress and Traffic flow
	- ![[attachments/Pasted image 20221001185529.png]]
	- We can say the backend API server receives ingress traffic from the website and has egress traffic to database server (port 3306).
	- From the database server's perspective it receives ingress traffic from the API server.
	- ![[attachments/Pasted image 20221001185748.png]]

> [!important]+ *Response from the database server to the request of api server is not considered as an egress traffic*. 
> - When deciding the type of traffic (ingress or egress) you only need to be concerned about the direction in which the request originates.
> - Only when an outbound connection is initiated by the pod it is considered as egress. 
> - Once you allow inbound traffic the response to it is allowed automatically and it not considered as egress traffic.
> - *Since the database pod doesn't initiate any outbound connection, there is no egress traffic*.


- *By default all the pods in ANY namespace can reach each other* using the **IPs or services or pod-names**.
	- k8s is configured with an **All Allow** rule by default which allows traffic from any pod to any other pod or services within the cluster.
- **Network policies are defined at the namespace level**.
- They **work on layer 3 and layer 4**.

> [!note] A network policy is like a **pod level firewall**.

- By default all the three pods can communicate with each other.
	- ![[attachments/Pasted image 20221001190134.png]]
- We would not want the frontend web server to communicate with the database pod directly.
	- This is where we implement network policies to allow traffic to the DB server only from the API server.

> [!note]- Network policy is another resource in the k8s namespace just like pods, services etc.
> - We link a network policy to one or more pods and then define the rules within the network policy.
> ![[attachments/Pasted image 20221001190412.png]]
> ![[attachments/Pasted image 20221001190451.png]]

- We **use labels and selectors to link network policies to pods**.
- Sample network policy rule allowing traffic from the API pod to the database pod
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
  namespace: prod # indicates that Network policies are namespaced
spec:
  # associating the pod (which we want to protect) to the network policy
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    # pods from which ingress is allowed
    - podSelector:
        matchLabels:
          name: api-pod
    ports:
    - protocol: TCP
      port: 3306
```

- From the above network policy rule *now ONLY the API pods in the prod namespace can access the database pod in the prod namespace*.
	- **API pods (with the same labels) in different namespaces (like staging) will not be able to access the database pod in the prod namespace unless a `namespaceSelector` is specified allowing traffic from other namespaces**.
	- ![[attachments/Pasted image 20221001192932.png]]
	- Note here the labels in the `matchLabels` section of `namespaceSelector` are the *labels of namespace staging*. For a more clear example look at the [[KodeCloud - CKAD - Network Policies#Example namespaceSelector| example section]].
	- We have a [[KodeCloud - CKAD - Network Policies#Understanding rules | single rule with 2 conditions]].
		- This means we will have to take AND operation of both the rules implying *ONLY the API pods from the staging namespace can access the database pod in prod namespace*.
		- The API pods in the prod namespace CAN'T access the database pod in the prod namespace.

- If we don't have pod selector field in the definition file and just the namespace selector then all the pods in that namespace (in this case staging) will be able to access the database pod.
	- ![[attachments/Pasted image 20221001193045.png]]
	- Since we have specified the namespace selector to staging, prod API pods WON'T be able to connect to the database pod in prod.

- Now suppose we have a backup selector somewhere outside of k8s. 
	- It won't be able to access the database pod since it is not a pod within the cluster. So the podSelector and namespaceSelector fields are useless.
	- *But we can configure the network policy to allow traffic originating from certain IP addresses*.
	- ![[attachments/Pasted image 20221001193414.png]]

> [!question]- When would we need an egress rule for a database pod?
> If the database pod pushes backup to an external server then we would need egress rules.

> [!caution]- Network policies are *enforced by the network solution implemented* on the k8s cluster and NOT all network solutions support network policies.
> ![[attachments/Pasted image 20221001191702.png]]
> With a network solution provider that does not support network policies you can still create network policies but they won't be enforced.

- Generally **network policies WON'T work out of the box**. 
	- They *require network policy agents* to be effective.
	- Network policy agents enforce the network policies.
	- An example of installing calico on the EKS cluster
		- ![[attachments/Pasted image 20230505154839.png]]

## Understanding behaviour of network policies
- By default (without any network policy) all pods allow ingress and egress traffic on all the ports. 
- **Network policies only have allow rules** this means *when we create a network policy* of a particular type (egress or ingress) by default everything is denied unless we specify rules allowing traffic.
	- If we just apply ingress policy then all ingress traffic will be blocked apart from anything that we have allowed.
	- Applying an ingress policy won't affect egress policy and traffic can flow to everywhere until and unless an egress is specified.
		- ![[attachments/Pasted image 20230505160153.png]]
		- Egress is commented so only ingress is blocked

> [!important] Traffic is allowed if there is atleast one policy allowing it.

- You can choose to not specify ports if you want ingress or egress on all the ports.

## Understanding rules
- These are the 3 (*podSelector, namespaceSelector, ipBlock*) main selectors in the ingress block, same applies for the egress block.
	- **Instead of `from` we have `to` in egress**.

- These can be passed as individual rules or together as a single rule.
- In this example we have 2 rules.
	- ![[attachments/Pasted image 20221001193741.png]]
	- **It works as an OR operation**. 
	- Traffic originating from either of the locations is allowed.
- *But within the first rule we have 2 selectors which means the traffic must pass both of these criteria to reach the database pod*. 
	- It works like an **AND operation**.
	
> [!caution]+ *Now if we separate the namespaceSelector as a separate rule then this would mean that database pod can get traffic from almost any pod*.
> - Since it can get traffic from all the pods in the prod namespace.
> - It can get traffic from all the api pods in other namespaces.
> ![[attachments/Pasted image 20221001194301.png]]

## Example namespaceSelector
- **Current state**: 
	- There are 3 namespaces and there is a pod in each namespace. 
	- Due to the nature of k8s these pods can ping each other.
- **Target state**: 
	- Pod in namespace-c should not be able to ping pod in namespace-b.
	- ![[attachments/Pasted image 20230505153706.png]]
- **Solution**:
	- Confirming the label of namespace-a
		- ![[attachments/Pasted image 20230505153754.png]]
	- YAML file
		- ![[attachments/Pasted image 20230505154019.png]]

## Simple Deny configurations
- Deny all traffic:
	- ![[attachments/Pasted image 20230505160512.png]]
- Deny all ingress traffic:
	- ![[attachments/Pasted image 20230505160748.png]]
- Deny all egress traffic:
	- ![[attachments/Pasted image 20230505160725.png]]
- Allow all egress traffic:
	- ![[attachments/Pasted image 20230505160814.png]]
- Allow all ingress traffic:
	- ![[attachments/Pasted image 20230505160833.png]]

> [!note]- Points to note from the above policies 
> - `podSelector: {}` means select all the pods.
> - Empty arrays `- Ingress` and `- Egress` means no rules have been white listed hence denying all traffic.
> - The policies will only be *applied to a particular namespace*. 

## Commands
- Listing the network policies:
	- `k get netpol`

## References
- [Kubernetes Network Policy Tutorial - yaml explained + Demo Calico - YouTube](https://www.youtube.com/watch?v=u1KUft3fsCk)
- [Kubernetes Network Policy - YouTube](https://www.youtube.com/watch?v=v7_XvCW6U48)