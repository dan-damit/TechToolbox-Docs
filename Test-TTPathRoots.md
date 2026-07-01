# Test-TTPathRoots

## SYNOPSIS
Shows effective TechToolbox path roots and key resolved config paths.

## SYNTAX
```powershell
Test-TTPathRoots [[-EnsureDirectories]] [<CommonParameters>]
```

## DESCRIPTION
Returns the active module/home/log/export roots derived from environment variables and runtime defaults. Optionally creates the root directories if they do not exist.

The cmdlet resolves several key paths:
- **OneDrive Root**: Determined by checking `$env:OneDriveCommercial`, `$env:OneDrive`, `$env:OneDriveConsumer`, or falling back to `$env:USERPROFILE`.
- **Home Root (`$homeRoot`)**: Uses `$env:TT_Home` if set, otherwise defaults to `<OneDriveRoot>\TechToolbox`.
- **Module Root (`$moduleRoot`)**: Uses `$env:TT_ModuleRoot` if set, otherwise calls `Get-ModuleRoot`.
- **Logs Root (`$logsRoot`)**: Uses `$env:TT_LogsRoot` if set, otherwise defaults to `<homeRoot>\LogsAndExports\Logs`.

## PARAMETERS

### -EnsureDirectories
Creates the effective logs and exports root directories if they do not exist.

- **Type:** `System.Management.Automation.SwitchParameter`
- **Required?** false
- **Position?** named
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

## INPUTS
None. You cannot pipe objects to Test-TTPathRoots.

## OUTPUTS
`System.Management.Automation.PSCustomObject`
The cmdlet returns a custom object containing the resolved path information.

## NOTES
This command is primarily used for diagnostics and configuration verification within the TechToolbox environment.

## EXAMPLE 1
```powershell
Test-TTPathRoots
```
### Description
Displays the current effective TechToolbox path roots without creating any new directories.

## EXAMPLE 2
```powershell
Test-TTPathRoots -EnsureDirectories
```
### Description
Displays the current effective TechToolbox path roots and creates the logs/exports root directories if they do not already exist.