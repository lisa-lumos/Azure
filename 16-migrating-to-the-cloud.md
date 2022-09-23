# 16. Migrating to the cloud
Many organizations want to migrate their apps to the cloud for various motivations.
It has to be `planned ahead`, and done `methodically`, because rushing to the cloud could end up badly. There’s a `well defined process` that should be followed in order for the migration to complete successfully. 

## Six steps of the migration process
Motivation Assessment -> System Assessment -> Migration -> App Enhancements -> Testing -> Go Live

## Motivation Assessment
Answer the grand question: Why do we want to migrate to the cloud? Possible Answers
- To save costs
- To take advantage of cloud capabilities (services, redundancy, scalability, etc)
- To modernize the system
- To attract new employees

Saving Costs is not always the case - sometimes cloud system can cost MORE than on-premises system. CapEx vs OpEx. A very thorough cost estimation should be conducted before moving to the cloud.

Take Advantage of Cloud Capabilities is a great motivation! (probably the best…), but you need to make sure the migrated system can actually use cloud capabilities. For example, legacy, single-session app that cannot be scaled will end up on a single VM, just like on-prem. 

Modernize the System - usually, Modernization = Rewrite, but why doing this? Possible answers are: Current app no longer supported on existing platforms, could be it is difficult to hire employees because lack of this knowledge on the market, or because of licensing costs. 

Attract New Employees - it is not a very good motivation, but it works… The organization has to be part of the cloud trend in order to attract new employees. 

## Migration Strategies
There are mainly three strategies: `Lift & Shift`, `Refactoring`, `Rewrite`. 

`Lift & Shift`: Install the app in the cloud “as-is”, without any changes, usually on a VM. Quick but less efficient. 

`Refactoring`: Modify the app a bit so it can run on managed services (ie. App Services)

`Rewrite`: Rewrite the app from scratch, usually in a modern platform, and take advantage of all cloud services

Note that these migrations are often `combined`, and one of the most popular approaches when migrating to the cloud is to from the lift and shift, it takes virtual machines and put them in the cloud just to make sure that everything works correctly, and then starting optimization. This optimization might require some rewrites for some models, other models will stay as they are. And from my experience, this is the best approach to begin with, lift and shift and then perform gradual optimization.

## System Assessment
The goal of system assessment to find out what is the best way to migrate the system to the cloud, by asking the right questions:
- What is the runtime platform of the system and on which OS? (.NET, Java, C++ etc.)
- How is it deployed today? (VM, Docker…)
- What database(s) is used?
- What is the authentication mechanism?
- Any interfaces to organizational systems?
- Any work with hardware? (dongles…)

If the runtime platform is C++, then refactor is not an option, it either needs a complete rewrite, or have to stay with VMs, because the platform doesn't support C++ managed apps. 

If there are any interfaces to organizational systems, they perhaps migration is not an option. Because after migrating to the cloud, the organizational systems  will not be accessble from the cloud, unless we are going to use communication mechanisms such as VPN, express route etc. 

If the authentication is based on OAuth2, then transition to Azure AD will be easy. However, if the authentication is based on a completely different protcol, or even some kind of proprietary protcol, then migration will be a lot more complicated. 

`Azure Migrate` is a central hub for assessing and migrating various resources. It uses platform-specific agents to discover the resources that can be migrated, and provides migration recommendations. It is free, and is good for low-level assessment (platforms used etc.) but won’t deal with other considerations, ie. Interfaces with organizational systems. It can be used as a first-step, but must perform manual assessment afterwards. 

## Migration
Migration strategy depends on the system assessment results. 

`Lift & Shift`: The easiest strategy. Simply create VM images and build new VMs in the cloud. Don’t forget to put VMs in appropriate VNet and define NSG. 

`Refactoring`: Should be done in two phases: Lift & Shift – Make sure the app works in the cloud as-is. Refactor – gradually improve the system and integrate cloud
services. DDon't try to go straight to refactoring before making sure that the lift and shift actually works.

`Rewrite`: Not really a migration… Design the system from the ground up to be cloud native, and use as many cloud services as possible. 

DB Migration. In general: Prefer the managed version of the DB (and not on a VM). Migration is better done using the DB’s own tools and not via Azure migration services. Every database has its own migration tools, whether using export, import,  backup, etc.

## App Enhancements
After migration, the next step is to enhance the system. This not necessarily requires code changes. Main areas for improvements are:
- Logging and Monitoring using Azure Monitor
- Network protection using Application Gateway

## Testing
- Testing in cloud is similar to on-prem
- Put strong emphasis on logging and monitoring
- Make sure you have access to system’s data
- If system is auto-scaled or DRed – test these scenarios
- Check performance – might vary from the on-prem figures

## Go Live
Keep an eye on the costs: 
- Set up budget alerts
- Define tags
- Look at the data at least once a month





