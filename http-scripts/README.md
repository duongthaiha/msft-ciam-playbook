# CIAM Tenants HTTP Scripts

This project contains a set of HTTP scripts for interacting with the CIAM Tenants API. The scripts are designed to perform various operations such as creating, retrieving, listing, updating, and deleting tenants.

## Prerequisites

Before using the HTTP scripts, ensure you have the following:

- An Azure subscription
- Permissions to create and manage objects including resource groups and aad b2c tenants
- An application registered in Azure AD to support device login
  - API permission of https://management.azure.com/user_impersonation
  - Public client enabled (off by default)

## Device login
This uses the device login workflow to support user impersonation. You will need to run the first http call and get the device code and url from the response, use the info to authenticate, and then run the second http call.

## Core capabilities

Generic functionality showcased:
- login: Use the device login flow to act as yourself on Azure
- newrg: Creates a new resource group

[Tenant operations](tenants.http):
- checkName: Check if a tenant name exists before you create a tenant
- newTenant: Creates a new tenant, expect this to take at least 10 mins
- getTenant: Retrieves information about a specific tenant
- updateTenant: Updates the properties of a specific tenant
- listTenantrg: Lists all tenants within a specific resource group
- deleteTenant: Deletes a specific tenant

[Guest usages](guest-usages.http):
- newGuestUsage: Creates a new guest usage
- getGuestUsage: Retrieves information about a specific guest usage
- updateGuestUsage: Updates the properties of a specific guest usage
- listGuestUsagesrg: Lists all guest usages within a specific resource group
- deleteGuestUsage: Deletes a specific guest usage

[Graph API operations](graph-core.http):
- loginWithCredentials: User credential-based login to Entra External Identity (device code or password flow)
- listUsers: List users in the directory using Graph API
- addUserToGroup: Add a user to a group
- getUser: Get details of a specific user
- listGroups: List available groups
- createUser: Create a new user in the directory
- createGroup: Create a new group in the directory
- updateUser: Update user properties
- getCurrentUser: Get current authenticated user's profile

## Graph API Module (graph-core.http)

The Graph API module enables user authentication and Graph API operations specifically for Entra External Identity tenants. This module supports two authentication flows:

### Authentication Options

**Device Code Flow (Recommended):**
- More secure as passwords are not stored in scripts
- User authenticates via browser using device code
- Suitable for interactive scenarios

**Resource Owner Password Credentials (ROPC) Flow:**
- Direct username/password authentication
- Less secure but useful for automation scenarios
- Requires special app configuration

### Prerequisites for Graph API Module

- Entra External Identity tenant
- User account with appropriate permissions (User Administrator or Global Administrator role)
- Application registered in Entra External Identity with:
  - API permissions: `User.Read.All`, `GroupMember.ReadWrite.All`, `Group.Read.All`, `Directory.Read.All`
  - Public client flows enabled (for device code flow)
  - For ROPC flow: Enable "Allow public client flows" and configure Resource Owner Password Credentials grant

### Supported Operations

- **Authentication**: Device code flow or username/password authentication
- **User Management**: List, get, create, and update users
- **Group Management**: List groups, add users to groups, remove users from groups
- **Profile Operations**: Get current user profile and application information

## Ways to add variables
As you don't want to have hardcoded values about your azure environment in your source control environment, whilst the addition of the values at the top of the script with allow you to test quickly, I recommend either a `http-client.env.json` file or if you're using the recommended extension, adding the values to the `$shared` (or other) environment in the settings for the extension.

### At the top of the script
You can simply create these variables in the top of the file you want to work with:
```http
# For Azure Management API operations (tenants.http, guest-usages.http)
@tenantId=<your-tenant-id>
@clientId=<your-client-id>
@subscriptionId=<your-subscription-id>
@resourceGroupName=<your-resource-group-name>
@newTenantName=<your-new-tenant-name>

# For Graph API operations (graph-core.http)
@externalTenantId=<your-entra-external-identity-tenant-id>
@clientId=<your-app-client-id>
@username=<your-username>
@password=<your-password>
@userIdToAddToGroup=<user-object-id>
@groupId=<group-object-id>
@userIdToGetDetails=<user-object-id>
@userIdToUpdate=<user-object-id>
@externalTenantDomain=<your-tenant-domain.onmicrosoft.com>
```

### In `http-client.env.json`
Make a file in the same directory (or a parent) of the file you want to work with:
```json
{
    "dev": {
        // For Azure Management API operations
        "tenantId": "<your-tenant-id>",
        "clientId": "<your-client-id>",
        "subscriptionId": "<your-subscription-id>",
        "resourceGroupName": "<your-resource-group-name>",
        "newTenantName": "<your-new-tenant-name>",
        
        // For Graph API operations
        "externalTenantId": "<your-entra-external-identity-tenant-id>",
        "username": "<your-username>",
        "password": "<your-password>",
        "userIdToAddToGroup": "<user-object-id>",
        "groupId": "<group-object-id>",
        "userIdToGetDetails": "<user-object-id>",
        "userIdToUpdate": "<user-object-id>",
        "externalTenantDomain": "<your-tenant-domain.onmicrosoft.com>"
    }
}
```

### In the extension settings
You can add the variables to the `$shared` environment in the settings for the extension.
```json
"rest-client.environmentVariables": {
        "$shared": { 
            "tenantId": "<your-tenant-id>",
            "clientId": "<your-client-id>",
            "subscriptionId": "<your-subscription-id>",
            "resourceGroupName": "<your-resource-group-name>",
            "newTenantName": "<your-new-tenant-name>"
    }
}
```


## Notes

- If you're unfamiliar with HTTP files, read [HTTP files](https://learn.microsoft.com/en-us/aspnet/core/test/http-files?view=aspnetcore-9.0).
- Ensure that you handle the responses appropriately and check for any errors returned by the API.
- Refer to the [CIAM Tenants API documentation](https://learn.microsoft.com/en-us/rest/api/activedirectory/ciam-tenants?view=rest-activedirectory-2023-05-17-preview) for detailed information on each operation and required parameters.
- Using the extension humao.rest-client enables transpiling these requests into different languages
- Do make sure to check out the docs for the extension to see if other functionality is useful to you
