# Invoke-RestartService

## SYNOPSIS
Restarts a named Windows service locally or on one or more remote computers.

## SYNTAX

### ServiceName (Default)
```powershell
Invoke-RestartService [-ServiceName] <String> [[-ComputerName] <String[]>]
   [-TimeoutSeconds <Int32>] [-Force] [-Credential <PSCredential>] [<CommonParameters>]
```

## DESCRIPTION
The `Invoke-RestartService` function performs a controlled restart of a specific Windows service. It utilizes the shared `Restart-ServiceWorker` logic to handle the actual restart operation.

### Workflow Summary
- Initializes TechToolbox runtime.
- Iterates through each target specified in `-ComputerName`.
- For local targets (current computer or localhost), it uses a direct local path.
- For non-local remote targets, it establishes a PowerShell session via `Start-NewPSRemoteSession` and executes the command remotely.
- Calls `Restart-ServiceWorker` with the provided parameters (`ServiceName`, `TimeoutSeconds`, `Force`).
- Cleans up any established remote sessions in finally blocks to ensure resources are released.
- Logs per-target progress and errors using `Write-Log`.

The function supports standard PowerShell confirmation behavior via `-WhatIf` and `-Confirm`. If a WhatIf or Confirm action blocks the restart for a specific target, that target is skipped while others may continue processing.

## PARAMETERS

### -ServiceName
Specifies the name of the Windows service to restart. This parameter is mandatory and is passed directly to `Restart-ServiceWorker`.

*   **Type:** System.String
*   **Required?** true
*   **Position?** 1
*   **Default value:** None
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -ComputerName
Specifies one or more computer names to target for the service restart. Defaults to the current computer (`$env:COMPUTERNAME`). This parameter accepts values from the pipeline.

Local targets are identified as:
- The current computer name (`$env:COMPUTERNAME`)
- `localhost`

Non-local targets are handled through remote PowerShell sessions initiated by `Start-NewPSRemoteSession` and executed via `Invoke-Command`.

*   **Type:** System.String[]
*   **Required?** false
*   **Position?** 2
*   **Default value:** $env:COMPUTERNAME
*   **Accept pipeline input?** true (ByValue)
*   **Accept wildcard characters?** false

### -TimeoutSeconds
Specifies the maximum number of seconds to wait for the restart operations to complete within the worker. If the operation exceeds this time, it may timeout.

*   **Type:** System.Int32
*   **Required?** false
*   **Position?** named
*   **Default value:** 30
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -Force
If specified, forces the restart operation. This may bypass certain confirmation prompts or graceful shutdown attempts depending on the underlying worker implementation.

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

## INPUTS
System.String. You can pipe a computer name to `Invoke-RestartService`.

## OUTPUTS
None. This function does not return any output by default, but it logs progress and errors.

## NOTES
- The function relies on the TechToolbox runtime being initialized correctly.
- Remote operations require appropriate permissions and firewall rules (WinRM) to be configured on the target machines.
- Ensure that the service name provided is valid for the target operating system.

## EXAMPLES

### Example 1: Restart a local service
```powershell
Invoke-RestartService -ServiceName "Spooler"
```
This command restarts the Print Spooler service on the local computer using default timeout settings.

### Example 2: Restart a remote service with custom timeout
```powershell
Invoke-RestartService -ServiceName "wuauserv" -ComputerName "Server01", "Server02" -TimeoutSeconds 60
```
This command restarts the Windows Update service on Server01 and Server02, waiting up to 60 seconds for each operation.

### Example 3: Restart a service with forced action
```powershell
Invoke-RestartService -ServiceName "BITS" -Force
```
This command forces the restart of the Background Intelligent Transfer Service on the local computer.