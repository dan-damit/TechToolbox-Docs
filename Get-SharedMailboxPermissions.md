# Get-SharedMailboxPermissions

## SYNOPSIS
Reports allowed permissions on a shared mailbox including Full Access, Send As, Send on Behalf, and optionally folder permissions.

## SYNTAX

### Default (Mandatory Identity)
```powershell
Get-SharedMailboxPermissions -Identity <String> [-IncludeFolderPermissions] [-Folder <String>] [-ExportCsv <String>]
```

## DESCRIPTION
The `Get-SharedMailboxPermissions` cmdlet reports the permissions assigned to a shared mailbox. It retrieves:
- Full Access permissions
- Send As permissions
- Send on Behalf permissions
- (Optional) Folder-specific permissions such as Calendar access

This command is useful for auditing and verifying who has access to specific shared mailboxes within an Exchange Online environment.

## PARAMETERS

### -Identity <String>
The identity of the shared mailbox. Acceptable values include:
- User Principal Name (UPN)
- Alias
- Email address

This parameter is **mandatory**.

| Property | Value |
| --- | --- |
| Required? | true |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -IncludeFolderPermissions [Switch]
Indicates that folder permissions (e.g., Calendar, Inbox) should be included in the report.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Folder <String>
The name of the folder to query for permissions when `-IncludeFolderPermissions` is specified. The default folder is **Calendar**.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | Calendar |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -ExportCsv <String>
The file path where the permission report will be exported as a CSV file.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## EXAMPLES

### Example 1: Get permissions for a shared mailbox with folder details
```powershell
Get-SharedMailboxPermissions -Identity "ap@contoso.com" -IncludeFolderPermissions -ExportCsv C:\Temp\ap-perms.csv
```
**Description:**
Retrieves Full Access, Send As, and Send on Behalf permissions for the shared mailbox `ap@contoso.com`, includes Calendar folder permissions, and exports the results to a CSV file at `C:\Temp\ap-perms.csv`.

## NOTES
- Requires connection to Exchange Online module.
- The cmdlet automatically initializes the TechToolbox runtime if not already connected.
