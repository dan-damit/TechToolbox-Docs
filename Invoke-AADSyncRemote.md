# Invoke-AADSyncRemote

## SYNOPSIS
Remotely triggers Azure AD Connect (ADSync) sync cycle (Delta/Initial) on a target server via PSRemoting.

## SYNTAX

```
Invoke-AADSyncRemote [-ComputerName <String>] [[-PolicyType] { Delta | Initial }] [[-Port] { 5985 | 5986 }] [-Credential <PSCredential>] [<CommonParameters>]
```

## DESCRIPTION
The Invoke-AADSyncRemote function creates a remote PSSession (using Kerberos or provided credentials) to the Azure AD Connect host. It validates that the ADSync module and service are available, then triggers a synchronization cycle using `Start-ADSyncSyncCycle`. The cmdlet supports `-WhatIf` and `-Confirm` for safe execution verification.

This function uses TechToolbox configuration for default values (such as DefaultPolicyType and DefaultPort) if not explicitly provided. It utilizes Write-Log for unified logging of actions and errors.

## PARAMETERS

### -ComputerName <String>
The FQDN or hostname of the Azure AD Connect server to target.

| Type | String |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | Derived from TechToolbox config (AADSync.ComputerName) if available. |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -PolicyType <String>
The type of synchronization cycle to trigger.

- Valid values: `Delta`, `Initial`
- Default value: Derived from TechToolbox config (AADSync.DefaultPolicyType). If not set in config, defaults to 'Delta'.

| Type | String |
| --- | --- |
| Required? | false |
| Position? | named |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -Port <Int32>
The WinRM port to use for the remote session.

- Valid values: `5985` (HTTP), `5986` (HTTPS)
- Default value: Derived from TechToolbox config (AADSync.DefaultPort). If not set in config, defaults to 5985.

| Type | Int32 |
| --- | --- |
| Required? | false |
| Position? | named |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -Credential <PSCredential>
The credentials to use for the remote PSSession. If not supplied, Kerberos authentication is used.

| Type | PSCredential |
| --- | --- |
| Required? | false |
| Position? | named |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to Invoke-AADSyncRemote.

## OUTPUTS
None. Output is written to the Information stream via Write-Information/Write-Log.

## EXAMPLES

### Example 1: Trigger a Delta sync
```powershell
Invoke-AADSyncRemote -ComputerName aadconnect01 -PolicyType Delta
```
This command triggers a delta synchronization cycle on the server 'aadconnect01'.

### Example 2: Trigger an Initial sync with WhatIf
```powershell
Invoke-AADSyncRemote -ComputerName aadconnect01 -PolicyType Initial -WhatIf
```
This command simulates triggering an initial synchronization cycle on 'aadconnect01' without actually executing it.

## NOTES
- Requires appropriate permissions to create PSSessions and run ADSync commands on the target server.
- The Azure AD Connect service must be running on the target server.
- Uses TechToolbox configuration for defaults. Ensure `AADSync.ComputerName`, `AADSync.DefaultPolicyType`, and `AADSync.DefaultPort` are configured in your TechToolbox config if relying on defaults.

## LINKS
[TechToolbox](https://github.com/dan-damit/TechToolbox)
