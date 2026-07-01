# Copy-Directory

## SYNOPSIS
Copies a directory to another directory using Robocopy.

## SYNTAX

### Local (Default)
```powershell
Copy-Directory -Source <string> -DestinationRoot <string> [-ComputerName <string>] [-Local] [-Mirror] [-Credential <pscredential>] [<CommonParameters>]
```

### Remote
```powershell
Copy-Directory -Source <string> -DestinationRoot <string> -ComputerName <string> [-Mirror] [-Credential <pscredential>] [<CommonParameters>]
```

## DESCRIPTION
The Copy-Directory function copies a directory to another directory using Robocopy. It supports both local and remote execution via PowerShell Remoting.

It uses config-driven defaults for logging, flags, retries, and mirror behavior.

When copying to a remote computer, the source path must be accessible from the local machine (e.g., via UNC path) or the copy is initiated on the remote side if -Local is not specified. The destination root on the target machine will receive the source folder name as a subdirectory.

## PARAMETERS

### -Source <string>
The source directory to copy.

| Type | Description |
| --- | --- |
| `System.String` | Required. Specifies the path of the source directory. |

### -DestinationRoot <string>
The root destination directory where the source folder will be copied. The final destination will be DestinationRoot\SourceFolderName.

| Type | Description |
| --- | --- |
| `System.String` | Required. Specifies the root path of the destination. |

### -ComputerName <string>
The name of the remote computer to perform the copy on. If omitted, the copy is performed locally unless -Local is specified.

| Type | Description |
| --- | --- |
| `System.String` | Optional. Specifies the target computer name for remote execution. |

### -Local <switch>
Switch to force local execution of the copy. Even if ComputerName is provided, this switch can override behavior depending on implementation details, though typically it ensures the operation runs locally.

| Type | Description |
| --- | --- |
| `System.Management.Automation.SwitchParameter` | Optional. Forces local execution. |

### -Mirror <switch>
Switch to enable mirror mode (/MIR) for the copy, which deletes files in the destination that no longer exist in the source.

| Type | Description |
| --- | --- |
| `System.Management.Automation.SwitchParameter` | Optional. Enables mirroring behavior via Robocopy's /MIR flag. |

### -Credential <pscredential>
Optional PSCredential to use for remote connections.

| Type | Description |
| --- | --- |
| `System.Management.Automation.PSCredential` | Optional. Specifies credentials for authenticating the remote session. |

## INPUTS
None. You cannot pipe objects to Copy-Directory.

## OUTPUTS
The final destination path where the directory was copied.

## EXAMPLES

### Example 1: Local Copy
```powershell
Copy-Directory -Source "C:\Data\FolderA" -DestinationRoot "D:\Backup"
```
**Description:** Copies FolderA to D:\Backup\FolderA locally.

### Example 2: Remote Copy
```powershell
Copy-Directory -Source "C:\Data\FolderA" -DestinationRoot "D:\Backup" -ComputerName "Server01"
```
**Description:** Copies FolderA to D:\Backup\FolderA on the remote computer Server01.

## LINKS
[TechToolbox](https://github.com/dan-damit/TechToolbox)
