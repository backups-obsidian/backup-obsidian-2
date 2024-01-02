---
created: 2023-01-14 19:23
updated: 2023-12-19 19:56
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Managing Multiple Environments]] |
|-|

| Next: [[Terraform - Expressions]] |
|-|

---
## Functions
- Some of the common functions are 
	- `file`: read data from a file 
	- `length`: determine the number of elements in a list or map
	- `toset`: convert a list to a set
 - We can test functions using an interactive console.
	- We can use the terraform console using `terraform console`
	- Terraform console *loads the state associated to the configuration directory by default*.
	- It also loads variables in the configuration files.
	- ![[attachments/Pasted image 20230114192353.png]]
- Numeric functions
	- **If we are planning to pass a list to a function that takes numbers then we need to expand the list using `...`**.
	- ![[attachments/Pasted image 20230114192605.png]]
- String functions
	- ![[attachments/Pasted image 20230114192840.png]]
	- `split` is used to split a string based on the separator. It *returns a list*.
	- `join` is used to convert a list to a string.
	- ![[attachments/Pasted image 20230114192916.png]]
- List functions
	- ![[attachments/Pasted image 20230114193039.png]]
	- `contains` is case sensitive
 - Map functions
	- ![[attachments/Pasted image 20230114193129.png]]
	- We can provide a default argument to the `lookup` function
	- ![[attachments/Pasted image 20230114193207.png]]

### Use `templatefile` to dynamically generate a script
- We can use Terraform's `templatefile` function to **interpolate values into the script** (like AWS User data script) at resource creation time. 
	- This makes the script more adaptable and re-usable.

```hcl file:"user_data.tftpl" fold
#!/bin/bash

# Install necessary dependencies
sudo DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" dist-upgrade
sudo apt-get -y -qq install curl wget git vim apt-transport-https ca-certificates
sudo add-apt-repository ppa:longsleep/golang-backports -y
sudo apt -y -qq install golang-go

# Setup sudo to allow no-password sudo for your group and adding your user
sudo groupadd -r ${department}
sudo useradd -m -s /bin/bash ${name}
sudo usermod -a -G ${department} ${name}
sudo cp /etc/sudoers /etc/sudoers.orig
echo "${name} ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/${name}

# Create GOPATH for your user & download the webapp from github
sudo -H -i -u ${name} -- env bash << EOF
cd /home/${name}
export GOROOT=/usr/lib/go
export GOPATH=/home/${name}/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
go get -d github.com/hashicorp/learn-go-webapp-demo
cd go/src/github.com/hashicorp/learn-go-webapp-demo
go run webapp.go
EOF
```

- In the above example `${name}` and `${department}` are the template variables.
- Next we use the `variables.tf` file to define the `user_name` and `user_department` input variables, which the configuration uses to set the values for the corresponding template file keys.

```hcl file:"variables.tf" fold
variable "user_name" {
  description = "The user creating this infrastructure"
  default     = "terraform"
}

variable "user_department" {
  description = "The organization the user belongs to: dev, prod, qa"
  default     = "learn"
}
```

```hcl hl:7 title:"Using the template file with the arguments" fold
resource "aws_instance" "web" {
  ami                         = data.aws_ami.ubuntu.id
  instance_type               = "t2.micro"
  subnet_id                   = aws_subnet.subnet_public.id
  vpc_security_group_ids      = [aws_security_group.sg_8080.id]
  associate_public_ip_address = true
  user_data                   = templatefile("user_data.tftpl", { department = var.user_department, name = var.user_name })
}
```