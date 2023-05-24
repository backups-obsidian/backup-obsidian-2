---
created: 2022-10-15 10:10
updated: 2023-05-23 09:47
---
---
**Links**: 
- [[104 Linux Index]]
- [[../111 KodeCloud/KodeCloud - SSL-TLS | KodeCloud - SSL - TLS]]
- [[../111 KodeCloud/111 KodeCloud Index|111 KodeCloud Index]]

---
## Openssl
- Get the `openssl` version: `openssl version`

### Generating a set of public and private keys
- `openssl genrsa`: This command will just output the key to the terminal.

> [!note] **The key generated using `openssl genrsa` contains both the public and the private key**.

- To *save the key* we use: 
	- `openssl genrsa -out privatekey.pem`
- Default key size is generally *2048*. This can be set in the openssl configuration file.
	- `openssl genrsa -out privatekey.pem 4096`
- Generating a private key with encryption
	- `openssl genrsa -aes256 -out privatekey.pem`
	- This is generally *done for CA keys* since they need to be protected. 
	- We don't encrypt the server private keys since they are used by the application.
- **Extracting the public key from the key pair**:
	- `openssl rsa -in privatekey.pem -pubout -out publickey.pem`
- The most common format for openssl is `.pem`
	- The binary contents of the file is base64 encoded hence it is super easy to copy and paste.
- *Inspecting* the private key.
	- `openssl rsa -in privatekey.pem -noout -text`
- *Inspecting* the public key.
	- `openssl rsa -pubin -in publickey.pem -noout -text`

> [!question]- What is the difference between the public and the private key?
> - If we take a look at the private key it will contain the *modulus and the exponent* and a lot of other things. 
> - The *public key only includes the modulus and the exponent*.
> ![[attachments/Pasted image 20221031010924.png]]
> ![[attachments/Pasted image 20221031010822.png]]
	
- *We can keep on increasing the size of RSA keys for more security but it is not ideal for IOT devices*.
	- Key size of 512 is easily brute forcible and 2048 is also not safe.
- With **elliptic curves we can increase our security without increasing our memory footprint**.
- We can have *RSA or elliptic keys*.
- If we take a look at the contents of the RSA private key using `cat` it will say `--BEGIN RSA PRIVATE KEY--` in the beginning.

### Certificates & Generating Certificates
> [!important]+ Both **CSRs and self signed certificates are generated using the `req`** option.
> The only difference is the `-x509` option which is used to generate self signed certificates.

> [!note]+ **Generating a CSR from a private key**:
> - `openssl req -new -key privatekey.pem -out CSR.pem`
> - If we take a look at the contents of the CSR using `cat` it will say `--BEGIN CERTIFICATE REQUEST--`

^1a9128

> [!note]+ **Generating a Self Signed Certificate from a private key**:
> - `openssl req -x509 -key privatekey.pem -out CERT.pem`
> - If we take a look at the contents of the Certificate using `cat` it will say `--BEGIN CERTIFICATE--` in the beginning.

^8c3aec

- `req` stands for request. 
	- It is used to *create* and *process* **certificate requests**.
- `x509` is a standard format for defining public key certificates.

- Certificates are there to identify websites.
- The *websites are identified using* the **DN (Distinguished Name)** format.
	- DN format is a hierarchy of key value pairs.
- **CN (common name) has to be there in all the certificates**.
	- This has to *match the domain name* which we enter in the browser *or the Subject Alternative Name* (SAN certificates). **(DOUBT)**
	- Rest of the information like country, locality, company name etc is just for informational purpose. 

- The *details we enter while generating a CSR or a Self Signed Certificate* goes into the **subject field** of the certificate.

> [!important]- In case of self signed certificates *issuer* and *subject* will be the same.
> The official definition of self signed certificates is that the issuer field matches the subject field.

- Looking at the **contents of the certificate**:
	- `openssl x509 -in CERT.pem -noout -text`
- Extracting *specific features of information*:
	- `openssl x509 -in CERT.pem -noout -text -subject -issuer`
	- `openssl x509 -in CERT.pem -noout -text -dates`: Validity dates
- Viewing the **contents of the CSR**:
	- `openssl req -in CSR.pem -noout -text`
	- *There will be no issuer*.
- Sample *output of a self signed certificate*:
	- ![[attachments/Pasted image 20221031002415.png]]
- Whenever we generate a CSR or a self signed certificate we have to enter the *subject values* interactively. We can pass it as *an argument*.
	- `openssl req -new -key privatekey.pem -out CSR.pem -subj "/CN=website.com"`
	- For self signed certificates we have `-x509` instead of `-new`
	- Specifying just the CN to keep it simple.

> [!caution]- As we know that `.extension` doesn't mean anything in linux we can name our private key as `private.key`, CSR as `some.csr` and Cert as `some.crt` for easily distinguishing what is what. 
> openssl doesn't care about the extension it cares about the extension of the file.

### How to find that a private key file belongs to a particular certificate?
- This is done by matching the public key value in the private key and in the certificate or the CSR.
	- In particular we match the *modulus value*

- Modulus value of a CSR:
	- `openssl req -in CSR.pem -noout -modulus`
- Modulus value of a certificate:
	- `openssl x509 -in CERT.pem -noout -modulus`
- Modulus value of a private key:
	- `openssl rsa -in privatekey.pem -noout -modulus`

- The above technique won't work for elliptic curve keys.

> [!note] Notice that we have to use `req` to list the contents of a CSR whereas we use `x509` to list the contents of a certificate.

### Signing CSRs using a root CA private key
- **Signing a CSR using a private key** ^21249a
	- `openssl x509 -req -days 3650 -in some.csr -CA ca.cert -CAkey ca.key -out some.cert -CAcreateserial`
	- Here days is the validity of the certificate.

> [!tip] For the clients to trust the certificates signed by our CA we need to have the CA's public cert in the browser/devices.
---
### Example
- Sample certificate of `sarthaknarayan.tech`
	- ![[attachments/Pasted image 20221031003634.png]]
	- CN is a `*.sarthaknarayan.tech`
	- ![[attachments/Pasted image 20221031003727.png]]
	- ![[attachments/Pasted image 20221031003751.png]]

## References
- [How to create a valid self signed SSL Certificate? - YouTube](https://www.youtube.com/watch?v=VH4gXcvkmOY) - **Must Watch**
- [Create & sign SSL/TLS certificates with openssl - YouTube](https://www.youtube.com/watch?v=7YgaZIFn7mY)
- [Using OpenSSL With Ed Harmoush - YouTube](https://www.youtube.com/playlist?list=PLtO_OYBiEo6kzs6dzPQ8CFilqZ6UxZKto)
	- 1st three videos