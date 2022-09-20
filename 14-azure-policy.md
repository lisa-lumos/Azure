# 14. Azure Policy
Enforcing organizational standards and compliance at scale is not simple. It is quite important in the cloud, role-based access helps a little, but not enough. 

Examples of policy could be: all vms should be built in a specific region; Only specific types of VMs are allowed to be built; Tags must be specified on all resources in the resource group; App Services should only be accessible over https. 

Azure policy allows:
- Defining policies
- Assigning the policies to `scopes` (Subscription, Resource Group, specific resource)

It is free, aside from auditing guest machines. `Policies are evaluated when` a resouce is created/updated/deleted in a scope, when a new policy is assigned to a scope, when a policy is updated, and once every 24 hrs. 

As a result of a non-compliant resource: The resource change can be denied, logged, altered before the change, or altered after the change. And related resource will be deployed if the policy requires it. 

## Azure policy concepts
`Definition`: The policy definition - what is allowed, to what resources (e.g.: only specific VM sizes can be built)

`Initiative`: Logical grouping of definitions (e.g.: VM definitions)

`Assignment`: Assigning the definition or initiative to a scope (Subscription, Resource Group, individual resourcce)

`Effect`: What is the outcome regarding non-compliant resource

## Custom Policies
You can create your own policies if the built-in policies do not satisfies your needs. You will rarelly need to define custom policy. Custom policy definitions are JSON-based documents that describes various properties of the policy, and the rule. 













