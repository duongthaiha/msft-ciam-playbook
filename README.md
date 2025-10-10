# Micrsoft Entra External tenants runbooks

A set of runbooks for managing Microsoft Entra External tenants. These runbooks are designed to automate various operations such as creating, retrieving, listing, updating, and deleting external tenants.

## http-scripts
This project contains a set of HTTP scripts for interacting with the CIAM Tenants API. [http-scripts/README.md](http-scripts/README.md) provides detailed instructions on how to use these scripts.

## Bulk guest user invitation (PowerShell)

Script: `ps/inviteuser.ps1`

Invites external (guest) users in bulk using Microsoft Graph. Accepts a CSV containing at minimum:

```
Name,InvitedUserEmailAddress
Jane Partner,jane.partner@example.com
Dev Tester,dev.tester@example.org
```

### Prerequisites
* PowerShell 7+ (recommended) or Windows PowerShell 5.1
* Microsoft Graph PowerShell SDK (`Install-Module Microsoft.Graph -Scope CurrentUser`)
* Permissions: The signed-in account must be able to consent to (or already have) `User.Invite.All` (and `Group.ReadWrite.All` if adding to a group)

### Usage examples
Invite users and send emails:

```
pwsh ./ps/inviteuser.ps1 -CsvPath ./ps/userinvitation.csv -TenantId "contoso.onmicrosoft.com"
```

Invite users, skip existing guests, add to a group, and specify a custom message:

```
pwsh ./ps/inviteuser.ps1 -CsvPath ./ps/userinvitation.csv -TenantId "contoso.onmicrosoft.com" -AddToGroupObjectId "<groupObjectId>" -SkipExisting -CustomMessage "Welcome to Contoso partner portal" -RedirectUrl "https://myapplications.microsoft.com/"
```

Disable sending the invitation email (user created in pending acceptance state):

```
pwsh ./ps/inviteuser.ps1 -CsvPath ./ps/userinvitation.csv -SendInvitationMessage:$false
```

After execution, a log CSV is produced (path shown in script output) summarising status per email.

### CSV optional columns
You may optionally include `InvitedUserDisplayName`, `Message`, or `RedirectUrl` per row to override the script defaults.

### Verification
List guest users:

```
Get-MgUser -Filter "userType eq 'Guest'" | Select DisplayName,Mail,UserPrincipalName
```

Remove a test guest (cleanup):

```
Remove-MgUser -UserId <objectId>
```
