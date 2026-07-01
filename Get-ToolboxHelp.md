# Get-ToolboxHelp

## SYNOPSIS
Provides help information for TechToolbox public commands.

## SYNTAX
```
Get-ToolboxHelp [[-Name] <String>] [-List] [-ShowEffectiveConfig] [-AsJson] [-Examples]
```

## DESCRIPTION
Get-ToolboxHelp displays help information for TechToolbox commands and configuration in an interactive, formatted manner. The function supports multiple display modes:

- **Default (no parameters)**: Displays a brief note pointing users to PowerShell's native 'Get-Help' cmdlet for detailed command documentation.
- **List**: Displays all public exported commands grouped by verb.
- **Command Help**: Shows detailed help for a specific command with fuzzy matching.
- **Configuration**: Displays the effective configuration currently loaded.

When running in an interactive console, output is formatted with colors and tables. In non-interactive environments, output is redirected to the Information stream for better logging compatibility.

## PARAMETERS

### -Name <String>
The name or partial name of a command to display help for. Supports fuzzy pattern matching using wildcards. If multiple matches are found, suggestions are displayed.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | 1 |
| Default value | None |
| Accept pipeline input? | False |
| Accept wildcard characters? | true |

### -List <Switch>
Switch to list all public exported TechToolbox commands. Commands are grouped by verb and sorted alphabetically. Each command displays its associated synopsis when available.

Can be combined with `-AsJson` for structured output.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -ShowEffectiveConfig <Switch>
Switch to display the effective configuration currently loaded in the TechToolbox runtime. If no configuration is loaded, attempts to load config.json directly from the module Config directory.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -AsJson <Switch>
Output the results as JSON.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

### -Examples <Switch>
Display examples for the specified command.

| Property | Value |
| --- | --- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | False |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to Get-ToolboxHelp.

## OUTPUTS
System.String, System.Object[]
The function outputs formatted text or JSON depending on the parameters used.

## EXAMPLES

### Example 1: List all commands
```powershell
Get-ToolboxHelp -List
```
This command lists all public exported TechToolbox commands grouped by verb.

### Example 2: Get help for a specific command
```powershell
Get-ToolboxHelp -Name "Get-BatteryHealth"
```
This command displays detailed help information for the Get-BatteryHealth cmdlet.

### Example 3: Show effective configuration
```powershell
Get-ToolboxHelp -ShowEffectiveConfig
```
This command displays the current effective configuration loaded in the TechToolbox runtime.

## NOTES
This function is part of the TechToolbox module and relies on the runtime environment being initialized. It serves as a central hub for discovering and understanding available commands and settings within the TechToolbox ecosystem.

## RELATED LINKS
- Get-Help
- TechToolbox Module Documentation