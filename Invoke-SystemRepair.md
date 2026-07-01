# Invoke-SystemRepair

## SYNOPSIS
Invokes system repair operations on a local or remote computer.

## SYNTAX

### Default (Default)
```powershell
Invoke-SystemRepair [-RestoreHealth] [-StartComponentCleanup] [-ResetBase] [-SfcScannow]
   [-ResetUpdateComponents] [[-ComputerName] <String>] [-Local] [-Credential <PSCredential>]
   [-UseCredSSP] [-RepairSource <String>] [-RepairSourceIndex <Int32>] [-LimitAccess]
   [-OperationTimeoutMinutes <Int32>] [-WaitPollSeconds <Int32>] [<CommonParameters>]
```

## DESCRIPTION
The `Invoke-SystemRepair` function performs various system repair operations including DISM RestoreHealth, StartComponentCleanup, ResetBase, SFC /scannow, and Windows Update component reset. It supports both local and remote execution.

### Workflow Summary
- Initializes TechToolbox runtime.
- Iterates through each target specified in `-ComputerName`.
- For local targets (current computer or localhost), it uses a direct local path.
- For non-local remote targets, it establishes a PowerShell session via `Start-NewPSRemoteSession` and executes the command remotely.
- Executes the selected repair operations based on provided switches (`RestoreHealth`, `StartComponentCleanup`, etc.).
- Cleans up any established remote sessions in finally blocks to ensure resources are released.
- Logs per-target progress and errors using `Write-Log`.

The function supports standard PowerShell confirmation behavior via `-WhatIf` and `-Confirm`. If a WhatIf or Confirm action blocks the operation for a specific target, that target is skipped while others may continue processing.

## PARAMETERS

### -RestoreHealth
Run DISM RestoreHealth operation. This repairs the Windows image by restoring corrupted files from a source if available.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -StartComponentCleanup
Run DISM StartComponentCleanup operation. This removes unused versions of components to free up disk space.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -ResetBase
Run DISM ResetBase operation. This removes all superseded versions of components in the component store, effectively resetting the base image.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -SfcScannow
Run SFC /scannow operation. This scans the integrity of all protected system files and repairs corrupted files.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -ResetUpdateComponents
Reset Windows Update components. This stops the Windows Update services, renames the SoftwareDistribution and Catroot2 folders, and restarts the services.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -ComputerName
Specifies one or more computer names to target for the repair operations. Defaults to the current computer (`$env:COMPUTERNAME`). This parameter accepts values from the pipeline.

Local targets are identified as:
- The current computer name (`$env:COMPUTERNAME`)
- `localhost`

Non-local targets are handled through remote PowerShell sessions initiated by `Start-NewPSRemoteSession` and executed via `Invoke-Command`.

*   **Type:** System.String[]
*   **Required?** false
*   **Position?** 1
*   **Default value:** $env:COMPUTERNAME
*   **Accept pipeline input?** true (ByValue)
*   **Accept wildcard characters?** false

### -Local
Run operations locally instead of remotely. If specified, remote session logic is bypassed even if `-ComputerName` is provided.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -Credential
Specifies a user account that has permission to perform this action. The default is the current user.

*   **Type:** System.Management.Automation.PSCredential
*   **Required?** false
*   **Position?** named
*   **Default value:** Current User
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -UseCredSSP
Uses CredSSP authentication for WSMan remoting so remote DISM can access delegated network resources such as UNC repair sources.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -RepairSource
Specifies the path to a repair source (e.g., Windows installation media or UNC share) for DISM operations. If not specified, DISM will attempt to use Windows Update as the source.

*   **Type:** System.String
*   **Required?** false
*   **Position?** named
*   **Default value:** None
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -RepairSourceIndex
Specifies the index of the Windows image in the repair source to use. Valid values are between 1 and 999.

*   **Type:** System.Int32
*   **Required?** false
*   **Position?** named
*   **Default value:** 1
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -LimitAccess
Limits access to the repair source, preventing DISM from using Windows Update as a fallback. This is useful when you want to ensure only the specified source is used.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -OperationTimeoutMinutes
Specifies the maximum number of minutes to wait for each DISM/SFC operation to complete. If the operation exceeds this time, it may timeout.

*   **Type:** System.Int32
*   **Required?** false
*   **Position?** named
*   **Default value:** 60
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -WaitPollSeconds
Specifies the number of seconds to wait between polling for the status of long-running operations.

*   **Type:** System.Int32
*   **Required?** false
*   **Position?** named
*   **Default value:** 5
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

## INPUTS
System.String. You can pipe a computer name to `Invoke-SystemRepair`.

## OUTPUTS
None. This function does not return any output by default, but it logs progress and errors.

## NOTES
- The function relies on the TechToolbox runtime being initialized correctly.
- Remote operations require appropriate permissions and firewall rules (WinRM) to be configured on the target machines.
- Ensure that the repair source provided is valid for the target operating system if using `-RepairSource`.
- DISM operations can take a significant amount of time; adjust `-OperationTimeoutMinutes` accordingly.

## EXAMPLES

### Example 1: Run DISM RestoreHealth locally
```powershell
Invoke-SystemRepair -RestoreHealth
```
This command runs the DISM RestoreHealth operation on the local computer using default timeout settings.

### Example 2: Run SFC and Reset Update Components remotely
```powershell
Invoke-SystemRepair -SfcScannow -ResetUpdateComponents -ComputerName "Server01", "Server02"
```
This command runs SFC /scannow and resets Windows Update components on Server01 and Server02.

### Example 3: Run DISM with a specific repair source
```powershell
Invoke-SystemRepair -RestoreHealth -ResetBase -RepairSource "D:\sources\install.wim" -RepairSourceIndex 1 -LimitAccess
```
This command runs DISM RestoreHealth and ResetBase using the specified local image file as the source, limiting access to prevent Windows Update fallback.
