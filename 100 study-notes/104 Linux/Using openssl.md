---
created: 2022-10-15 10:10
updated: 2022-10-17 10:54
---
---
**Links**: 
- [[104 Linux Index]]
- [[../111 KodeCloud/KodeCloud - SSL-TLS | KodeCloud - SSL - TLS]]

---
- Get the `openssl` version: `openssl version -a`
- **Generating a key pair**: 
	- `openssl genrsa -out tutorial.key 2048`
	- Here 2048 is the key size.
	- It is both our private and public key.
	
> [!note]- The generated key pair contains the private as well as the public key

- **Extracting the public key from the key pair**:
	- `openssl rsa -in tutorial.key -pubout -out tutorial_public.key`
	
	- **DOUBTFUL: PEOPLE SAY THAT PUBLIC KEY SHOULD BE USED FOR GENERATING THE CSR**
	
- **Creating a CSR**:
	- `openssl req -new -key tutorial.key -out tutorial.csr`
	- We will be asked to fill some information like country name, state etc.
- **Verifying the information entered in the CSR**:
	- `openssl req -text -in tutorial.csr -noout -verify`
- Creating a self signed certificate by signing it with the private key.
	- `openss1 x509 -in tutorial.csr -out tutorial.crt -req -signkey tutorial.key -days 365`
	- Here days is the validity of the certificate.