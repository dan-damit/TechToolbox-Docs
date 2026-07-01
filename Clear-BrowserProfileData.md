# Clear-BrowserProfileData

## SYNOPSIS
Clears Chromium browser profile data for Chrome and/or Edge.

## SYNTAX
```powershell
Clear-BrowserProfileData [-Browser <String>] [[-Profiles] <String[]>]
    [-IncludeCookies [<Boolean>]] [-IncludeCache [<Boolean>]] [-SkipLocalStorage [<Boolean>]]
    [-KillProcesses [<Boolean>]] [-SleepAfterKillMs <Int32>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## DESCRIPTION
Performs a browser profile cleanup workflow for Google Chrome, Microsoft Edge, or both. The function follows these steps:

1. Loads runtime configuration from `settings.browserCleanup` in config.json.
2. Applies config-driven defaults only for parameters not explicitly supplied by the caller.
3. Resolves one or both target browsers (Chrome, Edge, or All).
4. Optionally stops browser processes before deletion to avoid file locks on cache and cookie databases.
5. Discovers Chromium profile folders beneath each browser's User Data path.
6. Optionally filters the discovered set to named profiles such as Default or Profile 1.
7. Clears cookies, cache, and optionally local storage by delegating to internal profile-cleanup helpers.
8. Logs each stage of the run via Write-Log.

### Config-Driven Defaults
When a parameter is omitted, the function attempts to read its default value from `settings.browserCleanup` in config.json. This includes:
- includeCache
- includeCookies
- skipLocalStorage
- killProcesses
- sleepAfterKillMs
- defaultBrowser
- defaultProfiles

Caller-supplied parameter values always take precedence over config.

### Safety
This function supports `-WhatIf` and `-Confirm`. Process termination and any downstream deletion work are guarded by ShouldProcess-aware flow so you can preview the intended browser/profile actions before they occur.

## PARAMETERS

### -Browser <String>
The target browser(s) to clear data for. Valid values: 'Chrome', 'Edge', 'All'.
- **Default value**: 'All'
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -Profiles <String[]>
The specific profile names to target (e.g., Default, Profile 1). If omitted, all profiles are targeted.
- **Default value**: None
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -IncludeCookies <Boolean>
Whether to include cookies in the cleanup. Defaults to $true unless overridden by config.
- **Default value**: True
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -IncludeCache <Boolean>
Whether to include cache in the cleanup. Defaults to $true unless overridden by config.
- **Default value**: True
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -SkipLocalStorage <Boolean>
Whether to skip clearing local storage. Defaults to $false unless overridden by config.
- **Default value**: False
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -KillProcesses <Boolean>
Whether to kill browser processes before clearing data. Defaults to $true unless overridden by config.
- **Default value**: True
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -SleepAfterKillMs <Int32>
The number of milliseconds to wait after killing processes before proceeding. Defaults to 1500 unless overridden by config.
- **Default value**: 1500
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -WhatIf [<SwitchParameter>]
Shows what would happen if the cmdlet runs. The cmdlet is not run.
- **Default value**: False
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -Confirm [<SwitchParameter>]
Prompts you for confirmation before running the cmdlet.
- **Default value**: False
- **Required?**: false
- **Position?**: named
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

## INPUTS
None. You cannot pipe objects to this function.

## OUTPUTS
None. This function does not generate any output.

## EXAMPLES
### Example 1: Clear all browser data for Chrome and Edge
```powershell
Clear-BrowserProfileData -Browser All
```
This command clears cookies, cache, and local storage for all profiles in both Google Chrome and Microsoft Edge. It kills the processes first to ensure no file locks.

### Example 2: Clear only cookies for a specific profile
```powershell
Clear-BrowserProfileData -Browser Chrome -Profiles Default -IncludeCache $false -SkipLocalStorage $true
```
This command clears only cookies for the 'Default' profile in Google Chrome, leaving cache and local storage intact.

## NOTES
- This function relies on internal helper functions for actual file deletion.
- Ensure you have appropriate permissions to delete browser profile data.
- Use `-WhatIf` to preview actions without making changes.

## LINKS
- Get-Help Clear-BrowserProfileData
- TechToolbox Documentation