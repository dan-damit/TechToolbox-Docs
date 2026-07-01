# Set-EmailAlias

## SYNOPSIS
Safely sets, removes, or promotes an SMTP alias on an on-prem AD user.

## SYNTAX

### Add (Default)
```powershell
Set-EmailAlias -Identity <String> -EmailAlias <String> [-MakePrimary] [-Remove] [-AllowedDomains <String[]>] [-FailIfInUse [<Boolean>]] [-Credential <PSCredential>] [-Server <String>] [<CommonParameters>]
```

### Remove
```powershell
Set-EmailAlias -Identity <String> -EmailAlias <String> -Remove [-AllowedDomains <String[]>] [-FailIfInUse [<Boolean>]] [-Credential <PSCredential>] [-Server <String>] [<CommonParameters>]
```

## DESCRIPTION
The `Set-EmailAlias` cmdlet manages SMTP proxy addresses for Active Directory users. It can add a new secondary alias, promote an existing secondary alias to primary (uppercase `SMTP:`), or remove an existing alias.

It ensures that exactly one primary SMTP address exists after changes are made. The cmdlet includes safety features such as:
- Preventing duplicate aliases within the same user object.
- Validating email format and checking against optional allowed domains.
- Avoiding collisions with other AD objects (if `FailIfInUse` is enabled).
- Idempotency: if no changes are required, no write operation is performed.

## PARAMETERS

### -Identity
The identity of the user object to modify. Accepts samAccountName, distinguished name (DN), GUID, or User Principal Name (UPN).

| Property | Value |
| --- | --- |
| Required? | true |
| Position? | named |
| Default value | None |
| Accept pipeline input? | True (ByPropertyName) |
| Accept wildcard characters? | false |

### -EmailAlias
The SMTP alias to add, promote, or remove. Format should be `user@domain.com`.

| Property | Value |
| --- | --- |
| Required? | true |
| Position? | named |
| Default value | None |
| Accept pipeline input? | True (ByPropertyName) |
| Accept wildcard characters? | false |

### -MakePrimary
If specified, ensures that the `EmailAlias` becomes the primary SMTP address for the user. This is done by promoting it to uppercase `SMTP:` and demoting any existing primary alias to lowercase `smtp:`.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Remove
If specified, removes the `EmailAlias` from the user's proxyAddresses attribute instead of adding or promoting it.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -AllowedDomains
An optional array of domain names that are permitted for the alias. If provided, the cmdlet will validate that `EmailAlias` belongs to one of these domains.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -FailIfInUse
If set to `$true` (default), the cmdlet will fail if `EmailAlias` is already present on a different AD object. If `$false`, it may proceed depending on other logic, but typically this switch enforces uniqueness across the directory.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | True |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Credential
A `PSCredential` object representing a user with permissions to query and update Active Directory. Useful for running the cmdlet as a domain admin without interactive prompts.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Server
Specifies the Domain Controller to target for LDAP queries and updates. This is useful when using `-Credential` to avoid referrals or ensure changes are made to a specific DC.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to `Set-EmailAlias`.

## OUTPUTS
System.Object. This cmdlet does not generate any output by default, but may return error records if validation fails or the operation is unsuccessful.

## NOTES
- **Idempotency**: If the requested alias already exists and matches the desired state (e.g., adding a secondary that is already there), no change is made to AD.
- **Primary Constraint**: The cmdlet ensures exactly one primary SMTP address. Promoting an alias will demote the current primary.

## EXAMPLES

### Example 1: Add a Secondary Alias
```powershell
# Prompt for domain admin credentials and add a secondary alias
cmdlet Set-EmailAlias at command pipeline position 1
Supply values for the following parameters:
Credential [PSCredential]: 
Identity: jsmith
EmailAlias: jane.smith@vadtek.com
```
This example prompts for credentials, then adds `jane.smith@vadtek.com` as a secondary SMTP alias to the user `jsmith`.

### Example 2: Promote an Alias to Primary
```powershell
Set-EmailAlias -Identity jsmith -EmailAlias jane.smith@vadtek.com -MakePrimary
```
This example promotes `jane.smith@vadtek.com` to be the primary SMTP address for `jsmith`. If another alias was primary, it is demoted.

### Example 3: Remove an Alias
```powershell
Set-EmailAlias -Identity jsmith -EmailAlias jane.smith@vadtek.com -Remove
```
This example removes `jane.smith@vadtek.com` from the user's proxy addresses. If this was the primary, another secondary will be promoted to primary.

### Example 4: Validate Against Allowed Domains
```powershell
Set-EmailAlias -Identity jsmith -EmailAlias jane.smith@contoso.com -AllowedDomains @('vadtek.com', 'contoso.com')
```
This example adds an alias only if it belongs to `vadtek.com` or `contoso.com`. If the domain is not in the list, the cmdlet will fail.

## RELATED LINKS
- [Get-Help Set-EmailAlias](https://techtoolbox.io/help/Set-EmailAlias)
- [Active Directory User Management](https://techtoolbox.io/docs/ad-users)