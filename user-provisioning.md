# New-OnPremUserFromTemplate

## Synopsis

Provisions a new on-premises Active Directory user account by copying attributes and group memberships from a template user.

## Description

Creates a new Active Directory user account with characteristics derived from a template user. This function automates the repetitive task of user provisioning by duplicating standard configurations, organizational assignments, and group memberships.

The function operates in the following sequence:

1. Locates and validates the template user (by identity or search criteria)
2. Derives the new user's naming convention (UPN, SAM account name) from config or explicit parameters
3. Verifies idempotency (ensures user doesn't already exist)
4. Creates the new AD user with enabled status and forced password change
5. Copies specified attributes from the template (description, department, company, office, manager, or custom attributes)
6. Configures proxy addresses with the primary SMTP address
7. Adds the new user to all non-excluded groups from the template

Naming derivation uses the TechToolbox configuration framework. If `-SamAccountName` or `-UpnPrefix` are not explicitly provided, the function calls `Resolve-Naming` to generate these values based on `GivenName` and `Surname`.

Template location supports two parameter sets:

- **ByIdentity**: Directly specify the template user (faster)
- **BySearch**: Find template via attribute filters (flexible for complex scenarios)

The function supports `-WhatIf` and `-Confirm` through `ShouldProcess`, allowing dry-run validation before actual modifications.

---

## Parameters

### `-TemplateIdentity`

Specifies the identity of the template user to copy from. Accepts any valid Active Directory identity format: `sAMAccountName`, `DistinguishedName` (DN), `ObjectSID` (SID), or `ObjectGUID` (GUID).

Used only with the **ByIdentity** parameter set. Mutually exclusive with `-TemplateSearch`.

---

### `-TemplateSearch`

Specifies a hashtable of `LDAP attribute=value` pairs to locate the template user. All criteria are combined with AND logic; the first matching user is used.

**Example:** `@{ title='Software Engineer'; company='Acme Corp' }`

Used only with the **BySearch** parameter set. Mutually exclusive with `-TemplateIdentity`. Useful when template identity is unknown but characteristic attributes are available.

---

### `-GivenName`

Specifies the first name (`givenName` attribute) of the new user.

**Mandatory.** Used in the naming derivation (`Resolve-Naming`) if `-SamAccountName` or `-UpnPrefix` are not provided.

---

### `-Surname`

Specifies the last name (`sn`/`surname` attribute) of the new user.

**Mandatory.** Used in the naming derivation (`Resolve-Naming`) if `-SamAccountName` or `-UpnPrefix` are not provided.

---

### `-DisplayName`

Specifies the display name (`displayName` attribute) of the new user.

**Mandatory.** This is the user-friendly name visible in address books and global distribution lists. Typically formatted as `"FirstName LastName"` or `"LastName, FirstName"` depending on organizational standards.

---

### `-TargetOU`

Specifies the DistinguishedName (DN) of the organizational unit where the new user will be created.

**Optional.** If omitted, defaults to the same OU as the template user. Use this parameter to override and place the user in a different OU than the template.

**Example:** `"OU=Engineering,OU=Users,DC=company,DC=com"`

---

### `-SamAccountName`

Specifies the `sAMAccountName` (pre-Windows 2000 logon name) for the new user.

**Optional.** If omitted, the value is derived by calling `Resolve-Naming` with `GivenName` and `Surname`. The `sAMAccountName` must be unique within the domain and typically follows organizational naming conventions (e.g., `FirstinitialsLastname` or `FirstnameLastname`, limited to 20 characters).

---

### `-UpnPrefix`

Specifies the UPN prefix (left side of `@`) for the new user's `UserPrincipalName`.

**Optional.** If omitted, the value is derived by calling `Resolve-Naming`. The full UPN is constructed as `UpnPrefix@{Tenant.upnSuffix}` where the UPN suffix is read from config. The UPN must be unique across all forests.

**Example:** `"john.smith"` → results in `"john.smith@company.com"` if `upnSuffix` is `"company.com"`

---

### `-CopyAttributes`

Specifies which attributes to copy from the template user to the new user.

**Optional.** Accepts an array of attribute names. Defaults include:

- `description`
- `department`
- `company`
- `office`
- `manager`

Attribute names are case-insensitive and mapped to LDAP names internally (e.g., `office` → `physicalDeliveryOfficeName`). Unknown attributes are treated as raw LDAP names and applied via the `-Replace` parameter.

If `-CopyAttributes` is explicitly provided, that list is used. Otherwise, the function checks config (`settings.naming.copyAttributes`) for a configured list, then falls back to the hardcoded defaults above.

> **Note:** The `manager` attribute is only copied if the template's manager value is a valid DN (matching `^CN=.+,DC=.+`).

---

### `-ExcludedGroups`

Specifies group names to explicitly exclude when copying group memberships.

**Optional.** Default is empty. Applied after the distribution/security group filtering as an explicit deny-list.

---

### `-AllowedSecurityGroups`

Specifies Security group names that are allowed to be copied from the template.

**Optional.** Default is `'Domain Users'`. By default, all Security groups are excluded from copy unless their name appears in this allow-list. Distribution groups are always copied.

---

### `-InitialPasswordLength`

Specifies the length of the auto-generated initial password.

**Optional.** Default is `16` characters. The password is generated with mixed case, numbers, and symbols (specifically 3 non-alphanumeric characters) to meet complexity requirements.

The initial password is displayed in the function output and forces `ChangePasswordAtLogon`, requiring the user to choose a new password at first logon.

---

### `-Credential`

Specifies the Active Directory credential under which all AD operations execute.

**Mandatory.** Typically a service account with appropriate AD permissions:

- Create User objects (`User-Force-Change-Password`)
- Modify user attributes
- Add members to groups
- Read template user and group properties

---

### `-Server`

Specifies a specific domain controller to target for AD operations.

**Optional.** Useful when:

- Avoiding replication latency (create + immediate modifications on same DC)
- Targeting specific datacenters or regions
- Working around temporary replication issues
- Consolidating operations during a change window

If omitted, AD operations use the default domain controller selection logic (typically the closest available DC).

---

### `-ShowSummary`

Displays a formatted summary of the result object to the host.

**Optional switch.** When provided, the function writes a human-readable `Format-List` summary to the console. The function still returns the result object to the pipeline for capture and further automation.

---

## Inputs

**None.** This function does not accept pipeline input. All parameters must be supplied explicitly.

---

## Outputs

**`System.Management.Automation.PSCustomObject`**

Returns a custom object with the following properties:

| Property | Type | Description |
|---|---|---|
| `UserPrincipalName` | `string` | Full UPN of the created user |
| `SamAccountName` | `string` | `sAMAccountName` of the created user |
| `DisplayName` | `string` | `displayName` of the created user |
| `TargetOU` | `string` | DistinguishedName of the OU where user was created |
| `CopiedAttributes` | `string[]` | List of attributes copied from template |
| `GroupsAdded` | `string[]` | List of group names the user was added to |
| `InitialPassword` | `string` | Auto-generated temporary password |

Use `-ShowSummary` to display an additional formatted host summary during interactive use.

---

## Examples

### Example 1 — Basic usage with template identity

```powershell
$cred = Get-Credential
$result = New-OnPremUserFromTemplate `
    -TemplateIdentity 'john.smith' `
    -GivenName 'Jane' `
    -Surname 'Doe' `
    -DisplayName 'Jane Doe' `
    -Credential $cred
```

Creates a new user "Jane Doe" based on the template user "john.smith". All other parameters (naming, target OU, attributes, groups) are derived from the template or config defaults.

---

### Example 2 — Template search with explicit overrides

```powershell
$cred = Get-Credential
$result = New-OnPremUserFromTemplate `
    -TemplateSearch @{ department='Engineering'; company='Acme' } `
    -GivenName 'Bob' `
    -Surname 'Johnson' `
    -DisplayName 'Bob Johnson' `
    -SamAccountName 'bobj' `
    -UpnPrefix 'bob.johnson' `
    -TargetOU 'OU=Engineering,OU=Users,DC=company,DC=com' `
    -CopyAttributes @('description', 'department', 'company') `
    -Credential $cred `
    -Server 'DC01.company.com'
```

Creates a new user using template search (flexible lookup), overrides naming and OU placement, specifies exact attributes to copy, and targets a specific DC.

---

### Example 3 — Dry run with WhatIf

```powershell
$cred = Get-Credential
$result = New-OnPremUserFromTemplate `
    -TemplateIdentity 'template.user' `
    -GivenName 'Alice' `
    -Surname 'Williams' `
    -DisplayName 'Alice Williams' `
    -ExcludedGroups @('Domain Users', 'Sensitive Group') `
    -InitialPasswordLength 24 `
    -Credential $cred `
    -WhatIf
```

Performs a dry-run to preview user creation and group assignments without making actual changes. Excludes two specific groups and uses a 24-character password. Useful for validation before production execution.

---

### Example 4 — Interactive use with ShowSummary

```powershell
$cred = Get-Credential
$result = New-OnPremUserFromTemplate `
    -TemplateIdentity 'template.user' `
    -GivenName 'Jamie' `
    -Surname 'Miller' `
    -DisplayName 'Jamie Miller' `
    -Credential $cred `
    -ShowSummary
```

Creates the user and returns a `PSCustomObject` to the pipeline while also writing a formatted summary to the host for interactive visibility.

---

## Notes

### Configuration Dependency

This function requires TechToolbox configuration to be loaded via `Initialize-TechToolboxRuntime`. The following config sections must be present:

- `settings.tenant.upnSuffix` — The primary UPN domain suffix (e.g., `"company.com"`)
- `settings.naming.copyAttributes` *(optional)* — Custom list of attributes to copy
- Naming resolution functions for deriving `SamAccountName` and `UpnPrefix`

### Idempotency

Before creation, the function checks if a user with the target UPN already exists. If found, the function logs a warning and returns without creating a duplicate. Plan accordingly when retrying failed provisioning operations.

### Required Permissions

The `Credential` account must have:

- Create User Objects on the target OU
- Reset Password permission (for setting initial password)
- Write access to user attributes being modified
- Read access to template user and all groups
- Add Members permission on target groups

### Naming Derivation

If `-SamAccountName` or `-UpnPrefix` are omitted, `Resolve-Naming` is called to derive these values. This function must exist and return an object with properties `.Sam` and `.UpnPrefix`. Consult your `Resolve-Naming` documentation for conformance to organizational naming standards.

### Group Copy Behavior

The function retrieves all groups from the template's `memberOf` property and evaluates each group category. Distribution groups are copied by default. Security groups are excluded by default unless the group name appears in `-AllowedSecurityGroups`. Any names in `-ExcludedGroups` are always skipped. If a group add fails, it is logged as a warning but does not stop provisioning. Check logs for partial group assignments.

### Manager Attribute

The `manager` attribute is only copied if the template's manager value is a distinguished name (DN). If the template's manager is stored in a different format or is empty, it is skipped.

### Proxy Addresses

A single primary `proxyAddress` is configured at creation time in the format `SMTP:UpnPrefix@UPN_SUFFIX`. Additional proxy addresses or secondary SMTP addresses must be added separately or via directory synchronization.

### Initial Password

The auto-generated password is returned unencrypted in the output object. The caller is responsible for secure delivery (e.g., encrypted email, secure portal). The password forces a reset on first logon via `ChangePasswordAtLogon`.

### Performance

When targeting a specific domain controller via `-Server`, replication latency between create and modify operations is eliminated, improving reliability with rapid provisioning scripts.

---

## Related Links

- [`Get-ADUser`](https://learn.microsoft.com/powershell/module/activedirectory/get-aduser)
- [`New-ADUser`](https://learn.microsoft.com/powershell/module/activedirectory/new-aduser)
- [`Set-ADUser`](https://learn.microsoft.com/powershell/module/activedirectory/set-aduser)
- [`Add-ADGroupMember`](https://learn.microsoft.com/powershell/module/activedirectory/add-adgroupmember)
- `Resolve-Naming`
- `Initialize-TechToolboxRuntime`
