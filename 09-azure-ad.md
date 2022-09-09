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

We are mostly interested in controling access to Azure resources by setting up users, groups, roles. You can use Azure AD to add authentication to your own apps, this can also be done via Azure AD B2C.

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
| Privileged Identidy Management (PIM) |  |  | ✅ |
| Price | Free | $6 user/month | 9$ user/month |

















