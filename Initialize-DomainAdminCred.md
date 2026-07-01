# Initialize-DomainAdminCred

## SYNOPSIS
Initializes the Domain Admin Credential in the session by loading from config/secrets or prompting the user.

## SYNTAX

```powershell
Initialize-DomainAdminCred [[-WhatIf] [-Confirm]] [<CommonParameters>]
```

## DESCRIPTION
The `Initialize-DomainAdminCred` function is a backward-compatible initializer that loads the Domain Admin credential into the current session. It retrieves the username from `config.json` and the password from `config.secrets.json` (protected by DPAPI). If the credentials are not found in the configuration files, it will prompt the user for input.

This function does NOT write passwords to `config.json`. It internally calls `Get-DomainAdminCredential -Persist -PassThru` to handle the actual credential loading and persistence logic.

## PARAMETERS

### -WhatIf
Shows what would happen if the cmdlet runs. The cmdlet is not run.

```text
Required?                    false
Position?                    named
Default value                None
Accept pipeline input?       False
Accept wildcard characters?  false
```

### -Confirm
Prompts you for confirmation before executing the command.

```text
Required?                    false
Position?                    named
Default value                None
Accept pipeline input?       False
Accept wildcard characters?  false
```

## INPUTS
None. You cannot pipe objects to this function.

## OUTPUTS
None. This function does not generate any output.

## NOTES
- This function is designed for backward compatibility with older scripts that relied on explicit credential initialization steps.
- It relies on `Get-DomainAdminCredential` for the actual credential retrieval mechanism.
- Ensure that `config.json` and `config.secrets.json` are properly configured in your environment before running this command if you wish to avoid interactive prompts.

## EXAMPLES

### Example 1: Initialize Domain Admin Credential
```powershell
# This command initializes the domain admin credential for the current session.
Initialize-DomainAdminCred
```
