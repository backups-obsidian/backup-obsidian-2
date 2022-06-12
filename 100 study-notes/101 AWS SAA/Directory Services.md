---
created: 2022-04-18 21:09
updated: 2022-05-02 12:48
---
---
**Links**: [[101 AWS SAA Index]]

---

## What is Microsoft AD
-   Found on any Windows Server with AD Domain Services
-   It is a database of objects: User Accounts, Computers, Printers, File Shares, Security Groups
-   It offers **centralised security management**, **create account**, **assign permissions**. The idea is that if I create a user named John then it should be accessible in all the machines. All the machines check with the domain controller for any login

## AWS AD Services
### AWS Managed Microsoft AD
- We can **create our own AD in AWS,** manage users locally and **supports MFA**
- We can **establish a trust connection/relationship with on premise AD**.
- The **users are shared between on premise AD and AWS AD.**

### AWS AD Connector
- Users are managed **only on the on-premise AD**.
- It is a directory Gateway (**proxy**) to **redirect to on-premise AD**.
- The only place we can manage users is the on-premise AD.

### AWS Simple AD
-   AD-compatible managed directory on AWS
-   **Cannot be joined with on-premise AD**
-   The idea is that if we **don’t have an on-premise AD** and **need an AD for AWS cloud** then this is the best solution.

> [!question]- Why use an AD in AWS
> The idea is that using AD we can create *EC2 instances* that are going to be *running windows*. These windows instances can join the domain controller for all the logins and credentials. This is why we would want to have an AD in AWS.

