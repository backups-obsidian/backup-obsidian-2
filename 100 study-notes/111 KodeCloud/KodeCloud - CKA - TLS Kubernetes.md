---
created: 2022-09-15 21:09
updated: 2023-05-24 09:02
---
---
**Links**: [[111 KodeCloud Index]]
**Recommended Reads**: 
- [[KodeCloud - SSL-TLS | How SSL-TLS works]]
- [[../104 Linux/Using openssl|Using openssl]]

---
## Kubernetes TLS
### Understanding server & client certificates
> [!note]- *All communication* between the kube-apiserver and other components of k8s is *encrypted using TLS certificates*.
> - Communication between the user and the kube-apiserver is also encrypted using TLS certificates.
> ![[attachments/Pasted image 20230523114503.png]]

- We need server certificates for server and client certificates for client.
	- **Servers**: *kubeapi-server*, *kubelet*, *etcd*
		- *kubelet exposes an HTTPs endpoint that the kube-apiserver talks to*.
		- ![[attachments/Pasted image 20221013142353.png]]
	- **Clients**: 
		- *admin/users*, *kube-proxy*, *kube-scheduler*, *kube-controller-manager*, *kubelet* → clients to api-server.
		- kube-apiserver → client to etcd, kubelet.
		- ![[attachments/Pasted image 20230523124854.png]]
		- ![[attachments/Pasted image 20230524075504.png]]

> [!note]+ **kubeapi-server is a client to the etcd server and the kubelet**. 
> - Kubeapi-server can *use the same keys* that it used as a server as its client keys **or** we *can generate a new pair* of certificates specifically for the kubeapi-server to talk to etcd and kubelet respectively.
> - Similarly for kubelet to talk to kube-apiserver we can use the same keys it used as server or generate new ones.

- Certificates grouped for easier understanding:
	- ![[attachments/Pasted image 20230523125024.png]]

- We **need a CA for signing all these certificates**.
	- k8s *requires us to have at least 1 CA for our cluster*.
	- The *CA will have its own private key and certificate (self signed)**.
	- We can have more than one. Like one for all the components and one for the etcd client (kubeapi-server) and server certificates.

### Generating Certificates for the cluster
- **Name**: `CN` stands for common name.
- **Group**: `O` stands for organization.

#### CA certificates
- Creating a key pair: `openssl genrsa -out ca.key 2048`
- Generating a CSR: `openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr`
- Self signing the CSR: `openssi x509 -req -in ca.csr -signkey ca.key -out ca.crt`
	- We can skip steps 2 & 3 in a single step for generating self signed certificates:
		- ![[../104 Linux/Using openssl#^8c3aec]]

#### Client Certificates
> [!note]- `CN` can be anything but it is the name that will be logged.
> For example in case of user this is the name that will be logged in the audit logs when we use kubectl.

- **Admin User**:
	- Creating a key pair: `openssl genrsa -out admin.key 2048`
	- Generating a CSR: `openssl req -new -key admin.key -subj "/CN=kube-admin/O=system:masters" -out admin.csr`
		- **`system:masters` is a group with administrative privileges**.
		- This is what separates the admin user from other users.
	- Signing the CSR using CA's key: `openssl x509 -req -in admin.css -CA ca.crt -CAkey ca.key -out admin.crt`
- When we install the cluster using kubeadm it creates the signed admin configuration (`admin.conf` - creds to admin user) at `/etc/kubernetes`.
	- If we want use the cluster as an admin user we need to copy `admin.conf` to `~/.kube/config` on our system.
	- We can verify that it is indeed the certificate for the admin user by taking the contents of the certificate (from `admin.conf`), base64 decoding it and then viewing the details.
		- ![[attachments/Pasted image 20230524081347.png]]
- *Scheduler, controller manager, kube proxy are system components so their name (`CN`) must be prefixed with `system`*.
- **All the components need a copy of CA's certificate to verify each other**.

#### Server Certificates
- We generate certificates for etcd server in the same way as above.
	- We can have peers in etcd for HA and in that case we would need peer certificates.
	- *etcd can have its own CA*.
- **kube-apiserver**:
	- A lot of components talk to the kube-apiserver and it is known by different names like `kubernetes`, `kubernetes.default`, `kubernetes.default.svc`, `kubernetes.default.svc.local`, or by IP.
		- We can view these certificates by inspecting the certificate.
			- ![[attachments/Pasted image 20230523143341.png]]
	- *All these names must be present in the name (`CN`) of the CSR*.
	- For this we use an *openssl config file*.
		- ![[attachments/Pasted image 20230523140426.png]]
	- Specifying the certificates for the kube-apiserver
		- ![[attachments/Pasted image 20230523141013.png]]
- **Kubelet**:
	- Since kubelet runs on each node *we need a key certificate pair for each node of the cluster*.
	- All the certificates are stored at `/var/lib/kubelet/pki`
		- ![[attachments/Pasted image 20230524084218.png]]
	- The *kubelet **server** certificates are named after the nodes*.
		- ![[attachments/Pasted image 20230524082132.png]]
		- ![[attachments/Pasted image 20230524082251.png]]
	- The *kubelet **client** certificates for connecting to the api-server are named in the format `system:node:<nodename>`*.
		- The location to the kubelet client certificates can also be verified from `/etc/kubernetes/kubelet.conf`
			- ![[attachments/Pasted image 20230524084643.png]]
		- ![[attachments/Pasted image 20230524084836.png]]
		- ![[attachments/Pasted image 20230524084950.png]]
	- The nodes must be added to a group named `system:nodes`

> [!note]- You will see that the `kubelet.conf` has a single file for both certificate and key.
> - The certificate and the private key have been combined to form the `.pem` file.
> - We can verify it by using commands `openssl rsa -in kubelet-client-current.pem -text -noout` (used to view the contents of a private key) and `openssl x509 -in kubelet-client-current.pem -text -noout` (used to view the contents of a certificate).
> - Both the commands will work.

- Definition of `.pem` files
	- The single PEM file must contain the full entire certificate chain including the private key: the SSL server certificate, any necessary intermediate CA certificates, the root CA certificate, and private key.

> [!tip] In general location of certificate files for clients and servers can be found in their manifests (`/etc/kubernetes/manifests`) or in the service file definitions.

## Miscellaneous
- If we want to know which certificates are signed by which CA we can use `kubeadm certs check-expiration` if the cluster has been setup using kubeadm.
	- ![[attachments/Pasted image 20230524074447.png]]

## References
- [Kubernetes Certificates - YouTube](https://www.youtube.com/watch?v=9bLmmQFBE38)