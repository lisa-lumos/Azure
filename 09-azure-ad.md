# 9. Azure AD
`Azure AD`: `Azure Active Directory`. It is a central identity and access management cloud service. Can be used to manage access to thousands of apps (among them, the Azure Portal). It is secure, robust and intelligent. 

Advanced features:
- MFA
- Conditional access
- Device management
- Hybrid identity 
- Identity projection
- Monitoring and reports
- ...

We are mostly interested in controlling access to Azure resources by setting up users, groups, roles. You can use Azure AD to add authentication to your own apps, this can also be done via Azure AD B2C.

## Tenant
A `tenant` is a specific instance of Azure AD containing accounts and groups. It is also called `Directory`. It is not part of the subscription hierarchy - for new subscriptions, a new tenant is created automatically. A tenant can be assigned to multiple subscriptions. 

## Users and Groups
Users and groups are two of the 3 main objects managed by Azure AD (the third one is roles). They manage and store the users that are part of the tenant. 

Groups: groups the users in groups, e.g.: IT Admins, Developers, etc. It allows defining roles to groups instead of each user. 

Create user1 -> user1 has no subscription unless you add them to one

Create new guest user2 -> user2 has no subscription unless you add them to one

Create group1 -> add user1 into this group

## Azure AD Licenses
Azure AD licenses have great effect on the functionality and price of Azure AD, therefore it is important to understand the differences and recommend the right solution. 

|   | Free | Premium 1 | Premium 2 |
| ----------- | ----------- | ----------- | ----------- |
| Max Objects | 500k | Unlimited | Unlimited | 
| Users & Groups | ✅ | ✅ | ✅ |
| MFA | ✅ (All or nothing) | ✅ (With Conditional Access) | ✅ (With Conditional Access)|
| Dynamic Groups |  | ✅ | ✅ |
| Conditional Access |  | ✅ | ✅ |
| Risk Detection |  |  | ✅ |
| Risk based Conditional Access |  |  | ✅ |
| Privileged Identity Management (PIM) |  |  | ✅ |
| Price | Free | $6 user/month | 9$ user/month |

## Authentication
`Authentication` is divided into `3 factors`: Something you know (username/pwd, security question), Something you have (phone text message, smart card), Something you are (fingerprint, iris scan, etc).

When choosing a authentication type, need to consider `Feasibility` (not all devices have fingerprint scanner), `Ease of use` (username/pwd is quick), `Sensitivity` (biometric data is much more difficult to hack than pwd). 

### Two Factor Authentication / Multi Factor Authentication
For extremely sensitive systems (banks etc). 
- username/pwd + fingerprint
- phone + fingerprint
- etc.

## Azure AD Security Defaults
Free tier has preconfigured security settings at no additional cost:
- Require all users to use MFA
- Blocks legacy authentication
- ...

But if you need more fine-grained management, such as determine in which location/under which condition the user need to have MFA enabled, you need to use the Conditional Access that is available in the Premium 1 tier. 

## Role Based Access Control (RBAC)
If authorization is defined per user or per user group, then the access control is very granular and extremely hard to maintain. 

But in RBAC, `Authorizations` are associated to `Roles`, and `Users` are then attached to `Roles`. 

In Azure, in order to perform any operation, or access any data in Azure, such as create resource groups, access data in SQL, see metrics of App Service, you need to have `the appropriate role`. 

When creating a new subscription in Azure, you are `automatically` granted the highest role available, which is the owner. But for any other users, you have to define their roles in Azure. 

In general, there a 3 types of roles in Azure:
- `Owner` - Can perform any action on the resource, including assigning roles to it
- `Contributor` - Can perform any action on the resource, but cannot assign roles to it
- `Reader` - Can only view data, but cannot change anything

Examples: Virtual Machine Contributor, Cosmos DB Account Reader, Service Bust Data Owner. 

In RBAC, the Users/Groups are defined in the Azure AD Tenant, while the Roles and the Authorizations are defined in Azure itself. 

Note that it is best practice to assign roles to groups and not to individual users, for easy maintenance. 

## Managed Identities
We can assign Azure AD identity to Azure resources, the resource can then use this identity to connect to other Azure resources, therefore eliminating the need to handle credentials (usernames, pwds, etc.).

There are two types of Managed Identities: 
- System assigned - Managed by Azure, tied to the resource's lifecycle (when the resource is deleted, so is the identity). Used most commonly, easy to manage. 
- User assigned - Managed by the user. Can be assigned to multiple resources, not tied to any lifecycle. 

Resources that can be assigned Managed Identity:
- App Service
- Virtual Machine
- Event Grid
- Function
- ...

Resources that can be authorized using Managed Identity:
- SQL
- Event Hubs
- Service Bus
- Storage
- Key Vault
- ...

## OAuth2
It is a standard protocol for authentication & authorization. It is widely used, mainly in Web apps. 

OAuth2 components:
- User: The user who wants to access protected resources in the API
- Client App: The client app that the user uses to access the API
- Authorization Server: Authorizes the user for the client app
- Resource Server (API): the API being accessed, with resources the user want

Process: 
`Client App` calls the `API` anonymously without passing any identification data -> `API` sees no user data, so it tells the `Client App` to redirect to the `authorization server` -> the `Authorization server` asks the `user` to grant permission -> the `Authorization server` returns the access token to the `Client App` -> `Client App` sends the resource token to the `API`, now the `API` knows who the user is, and what is allowed for him. 

### App Registration
The `Authorization Server` should be familiar with the `Resource Server (API)`, therefore the `Resource Server` must register itself with the `Authorization Server`. 

### JSON Web Token (JWT)
It is the access token, a string containing the data the server needs in order to authenticate the user. 

It has 3 sections, that are base64 encoded, and concatenated with .:
- Header - contains type of token (JWT) and signing algorithm. 
- Payload - contains data about the user
- Signature

`jwt.io` allows you to construct or decode JWT. 

## Azure AD B2C (Business-to-Customer) service
It is an identity-as-a-service for your application, which enables integrating identity services in your app. It works with various identify providers and provides various customizable user flows. It can use various identity providers, such as Azure AD as the source of the user details. It is quite complex to set up and have a lot of moving parts. 

Identity services provided by Azure AD B2C:
- Sign up
- Sign in
- Log out
- Reset password
- ...

Authentication features:
- MFA
- Conditional access
- Audit log
- Custom policies
- Custom pages
- ...

## Syncing Azure AD with On Prem
This is useful when the organization has apps on prem and in cloud and wants to have a single user base, so that users won't need to log in twice, once in the cloud and once on prem. 

## Azure AD Connect
This is the solution. It is an agent connecting on prem directory with the Azure AD. 

Authentication with AD Connect:
- Password with Hash Sync. The passwords are copied to Azure AD, and the authentication happens in the cloud. If Azure AD is not available for some time, then the login is blocked. 
- Pass-through. The passwords stay on prem, Azure AD passes data to the on-prem directory for validation. The data is more protected since the pwds are not stored on the cloud. 





















