# Microsoft Entra External tenants runbooks

A set of runbooks for managing Microsoft Entra External tenants. These runbooks are designed to automate various operations such as creating, retrieving, listing, updating, and deleting external tenants, as well as managing users and roles through the Microsoft Graph API.

## http-scripts
This project contains a set of HTTP scripts for interacting with the CIAM Tenants API and Microsoft Graph API. [http-scripts/README.md](http-scripts/README.md) provides detailed instructions on how to use these scripts.

### Available modules:
- **tenants.http**: Azure Management API operations for CIAM tenant lifecycle management
- **guest-usages.http**: Guest usage operations for linking external tenants to subscriptions  
- **graph-core.http**: Microsoft Graph API operations for user authentication, user management, and role assignments in Entra External Identity