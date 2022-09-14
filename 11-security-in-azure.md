# 11. Security in Azure

## VM Security Best Practices
- Restrict access to the VM as much as possible
- Make sure only the `required ports` are open to the internet (22/1389/443/80)
- Limit access to `specific IP addresses` with possible
- Prefer using `Bastin` for accessing the VM, so no need for open ports, especially for SSH and RDP
- If the VM is not public facing, place it in a VNet that's not connected directly to the internet. 

## Networking Security Best Practices
- VNet that contains private resources only - should not be exposed to the internet
- Always use `NSG` to restrict access to subnets
- Use `Service Endpoint` / `Private Endpoint` to restrice access to resources
- Use the `Hub-and-Spoke` security model

## Database Security Best Practices
- Use `encryption` at rest and encryption at transit (usually On by default)
- Connect DB to relevant VNet using `Services Endpoint` / `Private Endpoint`
- Access DB from app using `Managed Identities`
- Use DB's `Firewall rule` to restrict external access

## App Service Security Best Practices
- Don't expose directly to the internet, use `Application Gateway`
- Connect to App Gateway's VNet using `Service Endpoint` / `Private Endpoint`
- Use `Azure AD` for Authentication, enforce with `MFA`
- Use `Managed Identity` to access other resources when possible. 

## KeyVault
Many apps have `secrets` that need to be kept safely - Connection strings, keys, certificates, API keys, ... They are usually kept in configuration files, configuration DB etc., and are not really secure. 

`KeyVault` saftly stores secrets of various types, and provides very restricted access - needs Azure AD authentication. It supports Hardware Security Modules for enhanced security, and is easily manageable and cost effective. KeyVault can be accessed via `REST API`. 

## Microsoft Defender for the Cloud
It is a central loc for monitoring and alerting security-related issues. It displays a summarized list of problems found in the subscriptions resources. In some cases, allows a single-click fix. So it is a good practice to take a look at it every once in a while. 











































