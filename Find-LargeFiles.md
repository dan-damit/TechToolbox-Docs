# Find-LargeFiles

## SYNOPSIS
Finds large files in specified directories.

## SYNTAX

### Default (Local)
```powershell
Find-LargeFiles [-SearchDirectory <String[]>] [-MinSizeMB <Int32>] [-Depth <Int32>] [-Export] [-ExportDirectory <String>] [-CsvDelimiter <String>] [-Credential <PSCredential>]
```

### Remote
```powershell
Find-LargeFiles -ComputerName <String> [-SearchDirectory <String[]>] [-MinSizeMB <Int32>] [-Depth <Int32>] [-Export] [-ExportDirectory <String>] [-CsvDelimiter <String>] [-Credential <PSCredential>]
```

## DESCRIPTION
The `Find-LargeFiles` cmdlet searches for files exceeding a specified size threshold in local or remote locations. It supports recursive searching with configurable depth and can export results to CSV.

By default, it uses configuration settings for the minimum file size (default 256 MB) and search directory. If no search directory is provided via parameter or config, the user will be prompted interactively.

## PARAMETERS

### -SearchDirectory <String[]>
The directories to search for large files. Multiple directories can be specified by passing an array or using semicolons in a string if read from input.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | true (ByValue) |
| Default Value | Configured `defaultSearchDirectory` or user prompt |

### -MinSizeMB <Int32>
The minimum size of files to find, in megabytes.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | false |
| Default Value | Configured `defaultMinSizeMB` (typically 256) |

### -Depth <Int32>
The depth of subdirectories to search. If not specified, it searches recursively.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | false |
| Default Value | Unlimited (recursive) |

### -Export
Indicates that the results should be exported to a CSV file instead of displayed in the console.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | false |
| Default Value | false |

### -ExportDirectory <String>
The directory where the exported CSV file will be saved. Only used when `-Export` is specified.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | false |
| Default Value | Current working directory |

### -CsvDelimiter <String>
The delimiter to use in the exported CSV file.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | false |
| Default Value | ',' (comma) |

### -ComputerName <String>
The name of the remote computer to search. If not specified, the local machine is searched.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | true (ByPropertyName) |
| Default Value | Local host |

### -Credential <PSCredential>
The credentials to use for remote operations. If not specified, current user credentials are used.

| Property | Value |
| --- | --- |
| Required? | false |
| Pipeline Input? | true (ByPropertyName) |
| Default Value | Current user |

## INPUTS
None. You cannot pipe objects to `Find-LargeFiles`.

## OUTPUTS
System.Object. The cmdlet outputs file system information objects or exports them to CSV if the `-Export` switch is used.

## NOTES
- If no search directory is provided via parameters or configuration, the user will be prompted to enter directories separated by semicolons.
- The minimum size default can be customized in the TechToolbox configuration under `largeFileSearch.defaultMinSizeMB`.

## EXAMPLES

### Example 1: Find large files locally
```powershell
Find-LargeFiles -SearchDirectory 'C:\Users' -MinSizeMB 100
```
This command searches for files larger than 100 MB in the C:\Users directory and its subdirectories.

### Example 2: Export results to CSV
```powershell
Find-LargeFiles -SearchDirectory 'D:\Data' -Export -ExportDirectory 'C:\Reports'
```
This command searches for large files in D:\Data and exports the results to a CSV file in C:\Reports.

### Example 3: Search remote computer
```powershell
Find-LargeFiles -ComputerName 'RemotePC01' -MinSizeMB 500
```
This command searches for files larger than 500 MB on the remote computer 'RemotePC01'.
