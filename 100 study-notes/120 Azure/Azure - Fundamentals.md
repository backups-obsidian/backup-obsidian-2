---
created: 2024-02-03 10:10
updated: 2024-02-04 10:29
---
---
**Links**: [[120 Azure Index]]

---
## Regions
- Areas of the world where Azure has a *set of datacenters* (minimum 3 Availability Zones in a set)
	- Regions are not necessarily "countries" but can be. For example we have 6 regions in US.
	- **Not all regions have AZ**.
- **Usually each region is connected to another region to make a "region pair"**.
	- Region pairs have *highest speed connections* and special treatment during Azure updates.
	- *Makes sense to put duplicates of the services in a region pair*.
	- Example of region pairs:
		- ![[attachments/Pasted image 20240203101618.png]]
- *Sovereign Azure regions are not connected to the Azure Public Cloud*.
	- They require approval to join / create a subscription.
	- They adhere to different compliance standards.
- *Three types of AZ Services*:
	- **Zonal Services**: We need to *choose a specific zone to deploy the service to*. E.g. *Virtual machines*.
	- **Zone Redundant Services**: Automatically *deployed across zones in a region*. E.g. *Azure SQL Database*.
	- **Always Available Services**: *Global services*. Microsoft takes care of managing them. E.g. Azure Portal, *Azure Active Directory*.

## Resources, Resource Groups, Subscriptions & Management Groups
- **Hierarchy of resources**:
	- ![[attachments/Pasted image 20240203114207.png]]
- A **resource is a generic word to represent an Azure service** that we have access to, such as a specific *Virtual Machine, Storage Account, or Database*.
	- We can create a resource in many different ways - Azure Portal, CLI, PowerShell, ARM Template, etc.
- *Each resource has a name given by us at the time of creation*.
- *A brand new subscription is created with NO resources*.
- Most resources have costs associated with them

> [!note] The resource is associated with **one (and only one) subscription**, to which its cost is billed.

- **Resource group is a logical grouping of resources**.
- Resource Group **associated with a region**, which *can be different than the resources it contains*.
- It is recommended that all services/resources in a resource group should have a *similar lifecycle* - deploy together, delete together.

> [!note] **All resources must belong to one and only one resource group**.

- Permissions can be assigned at the resource group level.
- Any resource group can communicate with another resource group.
	- There is no permissions preventing communication of one resource group with another.
- **A subscription is a billing unit within Azure**.
	- There is *always a payment method associated with a subscription*.
- Users can have access to more than one subscription, and different roles.
	- User can be an administrator in one, low lever user in another and so on.
- Different types of subscription plans:
	- Free plan - $200 credits first 30 days (*can only have one*)
	- Pay as you Go - billed to credit card
	- Enterprise Agreement - EA
	- Free credits - MSDN, Startup plans
- Some companies can choose to have *multiple subscriptions* because it can be used to **separate out business units** within an organization - e.g. Sales, IT, Finance Or **separate by geography**- e.g. North America, Europe, Asia.
	- ![[attachments/Pasted image 20240204102609.png | Example of a company using different subscriptions]]

> [!note]- The main purpose of subscription is to separate out the billing.
> It is entirely possible for a large organization to have a single subscription.

- **Management groups contain one or more subscriptions**.
	- ![[attachments/Pasted image 20240204102836.png]]