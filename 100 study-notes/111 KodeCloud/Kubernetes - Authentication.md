---
created: 2023-05-10 13:51
updated: 2023-05-10 16:20
---
---
**Links**: [[111 KodeCloud Index]]

---
## Authentication
> [!note] Kubernetes **assumes that users are managed outside of Kubernetes**.

- While Kubernetes defines the concepts of both user accounts and service accounts natively, it doesn’t provide us with a single, built-in method for authenticating those accounts.
- Different authentication methods are:

### X.509 Certificates 
- One of the *simplest authentication methods* in Kubernetes is to use an X.509 certificate to verify the identity of the user issuing a request.
	- ![[attachments/Pasted image 20230510150004.png]]
- To use X.509 authentication, you must first **create a private key** and a **Certificate Signing Request**, or CSR, for the user account you want to authenticate. 
- You can do this in a Unix-like operating system environment using these commands:

```bash
openssl genrsa -out example.key 2048  
openssl req -new -key example.key -out example.csr -subj "/CN=example/O=developers"
```

> [!note]- Meaning of `CN` and `O`.
> - In certificates `CN` stands for **common name** and `O` stands for **Organization**.
> - `CN` specifies the *name of the user* and `O` specifies the *group of the user*.

- Then, create a **self-signed certificate** using the CSR you generated and the certificate authority *(CA) associated with your Kubernetes cluster*.

```bash
openssl x509 -req -in example.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out example.crt -days 30
```

> [!question]- How to find the location of the CA cert?
> - Finding the api-server pod: `k get pods -n kube-system`
> ![[attachments/Pasted image 20230510153431.png]]
> - Describing the api-server pod: `k describe pod/kube-apiserver-controlplane -n kube-system`
> ![[attachments/Pasted image 20230510153716.png]]
> ---
> So the cert is present at `/etc/kubernetes/pki/ca.key` **in the control plane**.

> [!important] Alternatively we can also use **[[Kubernetes - Authentication#Using kubectl for signing CSR| kubectl to sign CSR]]**.

- Next, using kubectl, configure your cluster to use the key and certificate you have created:

```bash
kubectl config set-credentials example --client-key=example.key --client-certificate=example.crt
```

- Finally, define a context for the certificate:

```bash
kubectl config set-context example-context --cluster=<cluster-name> --user=example
```

> [!important]- Drawbacks of using X.509 certificate authentication
> - While X.509 authentication is straightforward to set up, the major drawback is that it **requires you to manage certificates manually**. 
> - When *certificates expire*, you’ll need to *regenerate them* in order to prevent disruptions to authentication. 
> - You will also need to *remember to revoke certificates for users* whose access you want to disable.
> - In short, while this authentication method is simple to configure, it entails a *lot of manual effort to manage at scale*. It may be appropriate for small-scale clusters where there are only a few users to authenticate, but it will not work well for production environments.

#### Using kubectl for signing CSR
- Base64 encoding the csr file: `cat example.csr | base64 | tr -d "\n"`
- Manifest for creating a certificate signing request

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
	name: example
spec:
	groups:
	- developer
	request: <base64 encoded csr certificate>
	signerName: kubernetes.io/kube-apiserver-client
	usages:
	- client auth
```

- After creating the singing request using `k create -f csr.yml` we should see a csr request using `k get csr`.
- The **csr will be in a Pending state**.
- Approving the CSR of a user as admin: `k certificate approve <csr-name>`
	- Once approved the csr will be in an Approved state.
- To get the certificate we use: `k get csr <csr-name> -o yaml`.
	- The certificate will be under `status -> certificate`
- The certificate is base64 encoded and needs to be decoded.
- [Kubernetes Tutorial For Beginners | Kubernetes User Authentication and Authorization - YouTube](https://www.youtube.com/watch?v=84j25sAUohA)
	- Quick recap.
- CSRs signed using kubectl manifests are automatically deleted after sometime.

###  OpenID Connect Tokens & Identity Provider
- A second method is to use OpenID Connect (OIDC) tokens. 
	- ![[attachments/Pasted image 20230510150100.png]]
- You can use one of many public OIDC providers, like Google or Okta.
- The exact implementation steps for OIDC vary depending on which OIDC provider you use; however, the key steps are as follows.
	- First, generate an *access_token*, *id_token* and *refresh_token* from your OIDC provider.
	- Second, edit your *kubeconfig file* with the *OIDC configuration details*.
- OIDC’s major advantage as a Kubernetes authentication method is that, once configured, it will work indefinitely. 
- However, it is relatively complex to set up because it requires generating access tokens using whichever process your OIDC provider supports, then manually writing configuration files that are tailored to those tokens and your provider.
- OIDC may be more than what is necessary for small-scale Kubernetes clusters, such as those that consist of only a handful of nodes or host just a single workload, *but it is worth the effort to configure if you have a large-scale cluster*.

###  Service account authentication token
- Service account token authentication uses signed bearer tokens to validate authentication requests.
- To use this method, first create a service account:

```bash
kubectl create serviceaccount account-name
```

- Creating a token (this will expire) for the service account:

```bash
k create token account-name
```

- We can then use the bearer token in HTTP headers to issue requests.
- This method is relatively simple to implement and does not require much ongoing maintenance. 
	- However, because *you have to create service accounts and associated tokens manually, the initial setup effort is high*. 
	- In addition, the **tokens are short-lived** and *must be refreshed* in order to keep access working.

### Static password files
- The simplest, but also **least secure**, authentication method for Kubernetes is to use static password files.
- To use this method, first generate a password file. 
	- Each line of the file should specify the *password, username and userID*, respectively, for the users you want to authenticate. 
	- Separate each value by commas, and create a new line for each new user.
- This method requires very little effort to configure. 
- But because it is based on a static, plaintext password file, *anyone who gains access to the file can easily modify it to gain unauthorized access to your Kubernetes cluster*.

## References
- [4+ Kubernetes Authentication Methods (Proxy, OIDC & More) | StrongDM](https://www.strongdm.com/blog/kubernetes-authentication)
- [Adding users to your Kubernetes cluster | Creating client certificate and key to provide users - YouTube](https://www.youtube.com/watch?v=XsFbUgZ0Ad8)