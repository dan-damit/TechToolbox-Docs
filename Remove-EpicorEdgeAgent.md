# Remove-EpicorEdgeAgent

## SYNOPSIS
Public wrapper that runs the Remove-EpicorEdgeAgent worker on one or more computers.

## SYNTAX

### Default (Default)
```powershell
Remove-EpicorEdgeAgent [[-ComputerName] <string[]>]
    [-Credential <PSCredential>]
    [-ThrottleLimit <int>]
    [-AsJob]
    [-PassThru]
    [-WorkerDir <string>]
    [-AnalyzeOnly]
    [<CommonParameters>]
```

## DESCRIPTION
Windows PowerShell 5.1-compatible remote runner. Uses Start-NewPSSession if available; otherwise falls back to New-PSSession (Microsoft.PowerShell endpoint). Streams progress and returns worker results with -PassThru.

The underlying worker script performs a deep removal of the Epicor Edge Agent, including:
- Stopping processes/services
- MSI uninstall via registry Uninstall keys (avoids Win32_Product)
- Removing scheduled tasks
- Deleting program/appdata folders
- Removing registry keys

## PARAMETERS

### -ComputerName
One or more target computers. Defaults to local computer.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | 1 |
| Default value | $env:COMPUTERNAME |
| Accept pipeline input? | true (ByPropertyName, ByValue) |
| Accept wildcard characters? | false |

### -Credential
PSCredential used for creating the PSSession (fallback path and Start-NewPSSession when supported).

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -ThrottleLimit
Reserved for future use; simple foreach currently.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | 8 |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -AsJob
Invoke the worker as a background job on each remote computer and return Job objects.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -PassThru
Emit the worker's result object(s) to the pipeline.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -WorkerDir
Path to the worker script. Defaults to ..\Workers\Remove-EpicorEdgeAgent.Worker.ps1

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -AnalyzeOnly
Sends -AnalyzeOnly to the worker (dry-run; no system changes).

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## INPUTS
System.String, System.Management.Automation.PSCredential

## OUTPUTS
System.Object (if -PassThru is used), System.Management.Automation.Job (if -AsJob is used)

## NOTES
- CmdletBinding(SupportsShouldProcess = $true, ConfirmImpact = 'High')
- Requires appropriate permissions on target computers.

## EXAMPLES

### Example 1: Remove Epicor Edge Agent from local computer
```powershell
Remove-EpicorEdgeAgent
```

### Example 2: Analyze only (dry-run) on remote computers
```powershell
Remove-EpicorEdgeAgent -ComputerName 'SRV01', 'SRV02' -AnalyzeOnly
```

### Example 3: Remove with specific credentials and pass through results
```powershell
$cred = Get-Credential
Remove-EpicorEdgeAgent -ComputerName 'SRV03' -Credential $cred -PassThru
```

### Example 4: Run as background job
```powershell
Remove-EpicorEdgeAgent -ComputerName 'SRV04' -AsJob
