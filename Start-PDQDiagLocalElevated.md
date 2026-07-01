# Start-PDQDiagLocalElevated

## SYNOPSIS
Open a new elevated PowerShell console (UAC), then run the local PDQ diag under SYSTEM.

## SYNTAX

```powershell
Start-PDQDiagLocalElevated [-LocalDropPath <String>] [-ExtraPaths <String[]>] [-ConnectDataPath <String>] [-StayOpen] [-ForcePwsh] [<CommonParameters>]
```

## DESCRIPTION
The Start-PDQDiagLocalElevated function spawns a new console with RunAs (UAC prompt). In that elevated console, it imports the TechToolbox module and calls the private Start-PDQDiagLocalSystem cmdlet. It captures a full transcript to C:\PDQDiagLogs\LocalRun_<timestamp>.log. On error, it writes detailed information and optionally pauses so you can read it.

## PARAMETERS

### -LocalDropPath <String>
Destination folder for the final ZIP.

- **Required?** false
- **Position?** named
- **Default value:** C:\PDQDiagLogs
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -ExtraPaths <String[]>
Additional files/folders to include in the diagnostic package.

- **Required?** false
- **Position?** named
- **Default value:** 
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -ConnectDataPath <String>
Root for PDQ Connect agent data.

- **Required?** false
- **Position?** named
- **Default value:** $env:ProgramData\PDQ\PDQConnectAgent
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -StayOpen [<SwitchParameter>]
Keep the elevated console open after it finishes (adds -NoExit and a prompt).

- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -ForcePwsh [<SwitchParameter>]
Prefer pwsh.exe explicitly; otherwise auto-detect pwsh then powershell.

- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

## INPUTS
None. You cannot pipe objects to Start-PDQDiagLocalElevated.

## OUTPUTS
None. This function does not return any output.

## NOTES
This command requires administrative privileges because it triggers a UAC elevation prompt.

## EXAMPLES

### Example 1: Run with default settings and stay open
```powershell
Start-PDQDiagLocalElevated -StayOpen
```
#### Description
Runs the PDQ diagnostic locally under SYSTEM context, keeps the elevated console window open after completion for review.

### Example 2: Include extra paths
```powershell
Start-PDQDiagLocalElevated -ExtraPaths 'C:\Temp\PDQ','D:\Logs\PDQ'
```
#### Description
Runs the PDQ diagnostic and includes additional files or folders from C:\Temp\PDQ and D:\Logs\PDQ in the collected data.