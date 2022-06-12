---
created: 2022-04-19 10:02
updated: 2022-04-20 10:49
---
---
**Links**: [[101 AWS SAA Index]]

---

- We can query a URL from the EC2 instance to get more information about the instance.
- The URL is `http://169.254.169.254/latest/meta-data`
- We can query details like the instance ID, **public IP** etc.

> [!caution] You can retrieve the IAM Role name from the metadata, but you **CANNOT retrieve the IAM Policy**.

- When a role is attached to the EC2 machine it gets temporary credentials. It queries these credentials from this end point and to execute commands from the CLI.
- We *can also use instance meta data query tool*. The Instance Metadata Query tool allows you to query the instance metadata without having to type out the full URI or category names.