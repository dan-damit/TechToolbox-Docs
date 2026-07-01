# Invoke-DownloadsCleanup

## SYNOPSIS
Cleans up old files from the Downloads folder.

## SYNTAX

```
Invoke-DownloadsCleanup [-ComputerName <String>] [-CutoffYear <Int32>] [-Local] [<CommonParameters>]
```

## DESCRIPTION
The Invoke-DownloadsCleanup function removes files older than a specified cutoff year from user Downloads directories. It can operate locally or on a remote computer via PSRemoting.

By default, it uses TechToolbox configuration settings for the cutoff year and dry run mode if not explicitly provided in the command line.

## PARAMETERS

### -ComputerName <String>
The name of the remote computer to clean up. If not specified, cleanup runs locally unless the `-Local` switch is used with a ComputerName.

| Type | String |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | Local machine if not specified and -Local is not used. |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -CutoffYear <Int32>
The year threshold for file deletion. Files older than this year will be removed.

| Type | Int32 |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | Derived from TechToolbox config (downloadsCleanup.cutoffYear). |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -Local <Switch>
Switch to force local cleanup even if ComputerName is specified.

| Type | SwitchParameter |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to Invoke-DownloadsCleanup.

## OUTPUTS
None. Output is written to the Information stream via Write-Information/Write-Log.

## EXAMPLES

### Example 1: Clean up local downloads older than 2020
```powershell
Invoke-DownloadsCleanup -CutoffYear 2020
```
This command removes files older than January 1, 2020 from the current user's Downloads folder.

### Example 2: Force local cleanup on a remote computer target
```powershell
Invoke-DownloadsCleanup -ComputerName "SERVER01" -Local
```
This command forces the cleanup to run locally on the machine where the script is executed, ignoring the ComputerName parameter for execution but potentially using it for configuration if applicable.

### Example 3: Clean up remote downloads with WhatIf
```powershell
Invoke-DownloadsCleanup -ComputerName "SERVER01" -CutoffYear 2015 -WhatIf
```
This command simulates cleaning up files older than January 1, 2015 on 'SERVER01' without actually deleting them.

## NOTES
- Requires appropriate permissions to access file systems locally or remotely.
- Uses TechToolbox configuration for default values (cutoffYear and dryRun). Ensure `downloadsCleanup.cutoffYear` is configured in your TechToolbox config if relying on defaults.
- Supports `-WhatIf` and `-Confirm` for safe execution verification.

## LINKS
[TechToolbox](https://github.com/dan-damit/TechToolbox)