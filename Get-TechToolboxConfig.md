# Get-TechToolboxConfig

## SYNOPSIS
Loads and validates a TechToolbox JSON configuration file.

## SYNTAX
```powershell
Get-TechToolboxConfig [-Path] <String> [<CommonParameters>]
```

## DESCRIPTION
The `Get-TechToolboxConfig` function reads a JSON configuration file from disk, converts it into a hashtable-based object graph, validates the required top-level and password-default settings, and returns the parsed configuration data.

The function maintains an in-memory script-scoped cache keyed by the resolved file path and the file's LastWriteTimeUtc value. If the same file is requested again and has not changed, the cached configuration is returned instead of re-reading and re-parsing the JSON.

### Behavior Details:
- Throws if the target file does not exist.
- Throws if the file is empty or contains invalid JSON.
- Uses `ConvertFrom-Json -AsHashtable` on PowerShell 7+.
- Uses a recursive PSCustomObject-to-hashtable fallback on Windows PowerShell 5.1.
- Validates that `schemaVersion` and `settings` exist at the top level.
- Validates that `settings.passwords.default` contains `separator`, `style`, `length`, and `digits`.
- Preserves raw values exactly as stored in the JSON and does not trim or mutate configuration values.

## PARAMETERS

### -Path <String>
The path to the TechToolbox JSON configuration file to load. The file must exist and contain valid JSON with the required TechToolbox configuration structure.

| Property | Value |
| --- | --- |
| Required? | true |
| Position? | 1 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to `Get-TechToolboxConfig`.

## OUTPUTS
System.Collections.Hashtable. Returns the parsed configuration as a nested hashtable/array structure.

## EXAMPLES

### Example 1
```powershell
Get-TechToolboxConfig -Path '.\Config\config.json'
```
Loads the main TechToolbox configuration file and returns the parsed settings.

### Example 2
```powershell
$config = Get-TechToolboxConfig -Path '$env:TT_ModuleRoot\Config\config.json'
$config.settings.passwords.default
```
Loads the configuration and inspects the default password settings.
