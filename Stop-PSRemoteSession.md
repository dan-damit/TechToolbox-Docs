# Stop-PSRemoteSession

## SYNOPSIS
Cleans up (removes) PSSessions created locally, with flexible targeting.

## SYNTAX

### BySession
```powershell
Stop-PSRemoteSession [-Session <PSSession[]>] [<CommonParameters>]
```

### ById
```powershell
Stop-PSRemoteSession [-Id <int[]>] [<CommonParameters>]
```

### ByInstanceId
```powershell
Stop-PSRemoteSession [-InstanceId <guid[]>] [<CommonParameters>]
```

### ByName
```powershell
Stop-PSRemoteSession [-Name <string[]>] [<CommonParameters>]
```

### ByFilter
```powershell
Stop-PSRemoteSession [[-ComputerName] <string[]>] [[-NamePrefix] <string>] [-OnlyDisconnected] [-OlderThanMinutes <int>] [<CommonParameters>]
```

## DESCRIPTION
The `Stop-PSRemoteSession` cmdlet removes one or more PSSessions from the current runspace. It provides multiple ways to target sessions, including by session object, ID, InstanceId (GUID), Name, ComputerName, or a name prefix.

It supports filtering for only disconnected/broken/closed sessions and sessions older than a specified number of minutes when using filter-based targeting.

This cmdlet uses `SupportsShouldProcess` to allow for dry-run testing via the `-WhatIf` parameter.

## PARAMETERS

### -Session
One or more PSSession objects to remove. Accepts pipeline input.

- **Required?** false
- **Position?** named
- **Accept Pipeline Input?** true (ByValue, ByPropertyName)
- **Default Value:** 

```powershell
Stop-PSRemoteSession -Session $sessionObject
```

### -Id
One or more session IDs as returned by `Get-PSSession`.

- **Required?** false
- **Position?** named
- **Accept Pipeline Input?** true (ByPropertyName)
- **Default Value:** 

```powershell
Stop-PSRemoteSession -Id 1, 2, 3
```

### -InstanceId
One or more session InstanceIds (GUIDs).

- **Required?** false
- **Position?** named
- **Accept Pipeline Input?** true (ByPropertyName)
- **Default Value:** 

```powershell
Stop-PSRemoteSession -InstanceId $guid1, $guid2
```

### -Name
One or more session names.

- **Required?** false
- **Position?** named
- **Accept Pipeline Input?** true (ByPropertyName)
- **Default Value:** 

```powershell
Stop-PSRemoteSession -Name "MySession"
```

### -ComputerName
Removes sessions whose ComputerName matches the specified value(s).

- **Required?** false
- **Position?** 0
- **Accept Pipeline Input?** true (ByPropertyName)
- **Default Value:** 

```powershell
Stop-PSRemoteSession -ComputerName "Server01"
```

### -NamePrefix
Removes sessions whose Name starts with this prefix. For example, use 'TT:' to remove TechToolbox-specific sessions.

- **Required?** false
- **Position?** 1
- **Accept Pipeline Input?** true (ByPropertyName)
- **Default Value:** 

```powershell
Stop-PSRemoteSession -NamePrefix "TT:"
```

### -OnlyDisconnected
When specified, only removes sessions that are in a Disconnected, Broken, or Closed state.

- **Required?** false
- **Position?** named
- **Accept Pipeline Input?** false
- **Default Value:** False

```powershell
Stop-PSRemoteSession -ComputerName "Server01" -OnlyDisconnected
```

### -OlderThanMinutes
When specified, only removes sessions that were created more than N minutes ago.

- **Required?** false
- **Position?** named
- **Accept Pipeline Input?** false
- **Default Value:** 0 (no age filter)

```powershell
Stop-PSRemoteSession -ComputerName "Server01" -OlderThanMinutes 60
```

## INPUTS
System.Management.Automation.Runspaces.PSSession, System.Int32, System.Guid, System.String

You can pipe PSSession objects to `Stop-PSRemoteSession`.

## OUTPUTS
None. This cmdlet does not return any output by default unless verbose or debug information is enabled.

## NOTES
This command uses `SupportsShouldProcess`, so you can use `-WhatIf` to see what would happen without actually removing the sessions, and `-Confirm` to be prompted before removal.

## EXAMPLES

### Example 1: Remove a specific session by object
```powershell
$session = New-PSSession -ComputerName Server01
Stop-PSRemoteSession -Session $session
```

### Example 2: Remove sessions by ID
```powershell
Stop-PSRemoteSession -Id 1, 2
```

### Example 3: Remove all TechToolbox sessions
```powershell
Stop-PSRemoteSession -NamePrefix "TT:"
```

### Example 4: Remove disconnected sessions from a specific computer older than 30 minutes
```powershell
Stop-PSRemoteSession -ComputerName "Server01" -OnlyDisconnected -OlderThanMinutes 30
```
