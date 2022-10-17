---
created: 2022-09-15 21:09
updated: 2022-10-13 20:38
---
---
**Links**: [[111 KodeCloud Index]]

---
- Asymmetric encryption: Public lock (anyone can lock it) & Private Key
	- ![[attachments/Pasted image 20220916174358.png]]
- Multiple users:
	- ![[attachments/Pasted image 20220916174455.png]]

> [!caution]- If you encrypt the data with you private key then anyone on the internet with the public key can decrypt your data.
> Either public or private key can be used to encrypt the data.


## SSL/TLS
- SSL/TLS uses both symmetric and asymmetric encryption
- We use asymmetric encryption to securely transfer the symmetric key from the client to the server.
- We *generate a public and private key on the server*.
	- We use the `openssl` command for this.
		- ![[attachments/Pasted image 20220916174914.png]]
- When the user tries to access the server using https it gets the public key from the server.
- **Now the user's browser generates and encrypts the symmetric key with the public key sent from the server**.
- Now the encrypted symmetric key is sent to the server and since the server has the private key it can decrypt and get the symmetric key.
- This symmetric key is then used to encrypt all information between the server and the user.

> [!note]- So asymmetric encryption is used to transfer the symmetric keys and the symmetric keys are used to encrypt the data.

- Now there is a possibility that a hacker sets up its own server to look like the bank and some how redirects your connection to its server.
	- ![[attachments/Pasted image 20220916175517.png]]
- Although we are communicating securely, we are communicating with the hacker's server.
- To tackle this problem when the *server actually sends a certificate that has the public key in it*.
- But anyone can generate a certificate. 
	- **The most important part the certificate is that who signs and issues the certificate**.
	- If you generate the certificate then you will have to sign it yourself, this is also known as *self signed certificate*.
	- When you try to use self signed certificates browser will try to warn you.
- We need CA (Certificate Authority) to sign and validate our certificates.

### Difference between Certificates and Keys
> [!question]+ What is the difference between a certificate and a key with respect to SSL? For example we use keys in SSH and certificates in SSL.
> - A **certificate contains a PUBLIC key**.
> - The certificate, *in addition to containing the public key*, **contains additional information such as issuer**, *what the certificate is supposed to be used for*, and other types of *metadata*.
> - Typically, a **certificate is itself signed by a certificate authority (CA) using CA's private key**. This verifies the authenticity of the certificate.

- CSR example:
	- Let us say company A has a key pair and needs to *publish his public key for public usage* (aka ssl on his web site).
	- Company A must make a certificate signing request (CSR) to a certification authority (CA) to *get a certificate for its (Company A) key pair*.
	- **The PUBLIC key, but NOT the private key, of company A's key pair is included as part of the certificate request**.
	- The CA then uses company A's identity information to determine whether the request meets the CA's criteria for issuing a certificate.
	- If the CA approves the request, it issues a certificate to company A. 
	- In brief **CA signs company A's PUBLIC key with his(CA's) PRIVATE key**, which verifies its authenticity.
	- **So company A's public key signed with a valid CA's private key is called company A's certificate**.

> [!caution]- **A certificate is just a "fancy" public key**, which is related to a private key. 
> *You can do the same thing with a certificate as you can do with a public key*.

### How to get your certificate signed by the CA
- We generate a *CSR (Certificate Signing Request)* using the *private key* we generated earlier and the domain name of the website.
	- ![[attachments/Pasted image 20220916180342.png]]
- The certificate authorities validate the information and if everything is ok they sign the certificate and send it back to us.
- We now have a certificate that the browsers trust. 
- If the hacker tried to get his certificate signed the same way he would fail during the validation phase so the website he is hosting won't have a valid certificate and you would get a warning when you try to visit the hacker's website since he would be using a self signed certificate.
	- ![[attachments/Pasted image 20220916180904.png]]
	- The only way in which the hacker could fool you is by signing the certificate with a custom CA and then somehow installing the CA's public key into your browser which is practically improbable.
- CAs use different techniques to make sure you are the actual owner of that domain.

> [!question]+ But how do the browsers know that the certificate was signed by a legitimate CA?
> - The CA's also have a pair of public and private key pairs.
> - They use their private keys to sign the certificates 
> - The public key of all the CAs are built into the browsers. 
> - *The browser uses this public key to validate if the certificate was actually signed by the CA*.

- Now in an organisation if you want to have SSL for internal websites and not get warnings when accessing them then we need to install a custom CA in all the client browsers.

- This whole process of generating, maintaining and distributing certificates is known as **PKI** (Public Key Infrastructure)
- Usually **certificates with public key have extension of `.pem` or `.crt`**.
- **Private certificates generally have an extension of `.key` or `*-key.pem`**.
	- ![[attachments/Pasted image 20220916183419.png]]

> [!note]- Private keys have the word key in them. Either in extension or in the name.

### Summary
- Admin uses an asymmetric pair of keys to secure ssh access to the server.
- Server uses an asymmetric key pair to secure HTTPS traffic.
	- But for this the server first sends a CSR to a CA.
	- The CA uses its private key to sign the CSR.
	- The signed certificate is sent back to the server.
	- The server now configures the web application with the signed certificate.
	- Now whenever the user access the web application the server first sends the signed certificate with its public key.
	- The user's browser uses the CA's public key to verify the signature of the certificate. 
	- It then generates a symmetric key and encrypts it with the server's public key and is sent back to the server.
	- The server uses its private key to decrypt and get the symmetric key.
	- This symmetric key is used for all communication going forward.
