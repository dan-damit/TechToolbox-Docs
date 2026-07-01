# Reset-WindowsUpdateComponents

## SYNOPSIS
Resets Windows Update components on a local or remote computer.

## SYNTAX

### Local (Default)
```powershell
Reset-WindowsUpdateComponents [[-ComputerName] <String>] [-Credential <PSCredential>]
```

## DESCRIPTION
The `Reset-WindowsUpdateComponents` function stops Windows Update services, renames update-related folders to clear cached data, and restarts the services. This process helps resolve issues related to Windows Update failures or corruption.

It supports both local execution (default) and remote execution via WMI/WinRM when a target computer name and credentials are provided.

## PARAMETERS

### -ComputerName
The name of the computer to reset Windows Update components on. Defaults to the local computer (`$env:COMPUTERNAME`).

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | 1 |
| Default value | $env:COMPUTERNAME |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Credential
Specifies a user account that has permission to perform this action. The default is the current user.

When specifying remote computers, credentials are required if running under an account without administrative privileges on the target machine.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | Current User |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to this cmdlet.

## OUTPUTS
None. This function does not generate any output.

## NOTES
- Requires administrative privileges on the target computer(s).
- Logs actions to a directory defined in the TechToolbox configuration (`$script:cfg.settings.windowsUpdate.logDir`).
- If the log directory does not exist, it is created automatically.

## EXAMPLES

### Example 1: Reset locally
```powershell
Reset-WindowsUpdateComponents
```
**Description:**
Resets Windows Update components on the local computer using default settings.

### Example 2: Reset remotely
```powershell
$cred = Get-Credential
Reset-WindowsUpdateComponents -ComputerName "SERVER01" -Credential $cred
```
**Description:**
Resets Windows Update components on a remote computer named 'SERVER01' using the provided credentials.

## RELATED LINKS
- [Get-Help Reset-WindowsUpdateComponents](https://example.com/help/Reset-WindowsUpdateComponents)
- [TechToolbox Documentation](https://example.com)