# Remove-Printers

## SYNOPSIS
Removes all printers from the system, with optional removal of ports, drivers, and per-user mappings.

## SYNTAX

### Default (Default)
```powershell
Remove-Printers [-IncludePorts] [-IncludeDrivers] [-Force] [-AllUsers] [-PassThru] [<CommonParameters>]
```

## DESCRIPTION
The `Remove-Printers` function uses Win32_Printer (CIM) to remove printer queues after resetting the print spooler and clearing the spool folder. It optionally removes TCP/IP ports and printer drivers. The function adds fallbacks for provider hiccups and frees common process locks (splwow64/PrintIsolationHost). It can also remove per-user network printer connections across all user profiles.

## PARAMETERS

### -IncludePorts
Also remove TCP/IP printer ports (non-standard).

- **Type:** System.Management.Automation.SwitchParameter
- **Aliases:** none
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -IncludeDrivers
Also remove printer drivers (after queues are gone).

- **Type:** System.Management.Automation.SwitchParameter
- **Aliases:** none
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -Force
Best-effort forced cleanup of driver packages via pnputil if standard removal fails.

- **Type:** System.Management.Automation.SwitchParameter
- **Aliases:** none
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -AllUsers
Attempt to remove per-user network printer connections for all user profiles.

- **Type:** System.Management.Automation.SwitchParameter
- **Aliases:** none
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -PassThru
Output a summary object with counts and failures.

- **Type:** System.Management.Automation.SwitchParameter
- **Aliases:** none
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

## INPUTS
None. You cannot pipe objects to Remove-Printers.

## OUTPUTS
System.Object. If -PassThru is specified, a summary object with counts and failures is returned.

## NOTES
This command has a high ConfirmImpact and supports ShouldProcess (WhatIf/Confirm).

## EXAMPLE 1: Remove all printers with ports, drivers, force cleanup, and per-user connections
```powershell
Remove-Printers -IncludePorts -IncludeDrivers -Force -AllUsers -PassThru
```
This command removes all printer queues, TCP/IP ports, and driver packages. It also forces a cleanup of driver packages using pnputil if standard removal fails and attempts to remove per-user network printer connections for all user profiles. A summary object is returned.

## EXAMPLE 2: Preview what would be removed
```powershell
Remove-Printers -WhatIf
```
This command shows what would happen if the cmdlet were executed without actually making any changes.