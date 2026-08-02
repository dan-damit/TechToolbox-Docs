## Set-PageFileSize.ps1 Documentation

### Overview

`Set-PageFileSize.ps1` is a PowerShell script that configures the initial and maximum page file size on a remote computer via PSRemoting. This script is part of the TechToolbox module and provides a convenient way to manage virtual memory settings across multiple machines.

### Synopsis

Sets the page file size on a remote computer.

### Description

The script connects to a remote computer using PowerShell remoting and configures the page file (virtual memory) with specified initial and maximum sizes. It supports both interactive prompts for missing values and credential handling based on configuration settings.

### Parameters

| Parameter | Type | Mandatory | Description |
|-----------|------|-----------|-------------|
| ComputerName | string | Yes | The name of the remote computer to configure |
| InitialSize | int | No | The initial page file size in MB. If not provided, user will be prompted |
| MaximumSize | int | No | The maximum page file size in MB. If not provided, user will be prompted |
| Path | string | No | The path where the page file will be created. Defaults to configured default path |
| Credential | PSCredential | No | Credentials to use when connecting to the remote computer. If not provided, falls back to the promptForCredentials setting in config |

### Usage Examples

```powershell
# Set page file with prompts for sizes
Set-PageFileSize -ComputerName 'Server01'

# Set page file with specific sizes
Set-PageFileSize -ComputerName 'Server01' -InitialSize 4096 -MaximumSize 8192

# Set page file with custom path and credentials
$cred = Get-Credential
Set-PageFileSize -ComputerName 'Server01' -Path 'D:\\PageFile' -InitialSize 2048 -MaximumSize 4096 -Credential $cred
```

### Configuration Settings

The script uses configuration settings from the TechToolbox config file:

- `settings.pagefile.defaultPath` - Default path for page file creation
- `settings.pagefile.minSizeMB` - Minimum allowed initial size (used for validation)
- `settings.pagefile.maxSizeMB` - Maximum allowed size (used for validation)
- `settings.defaults.promptForCredentials` - Whether to prompt for credentials if not provided

### Workflow

1. Validates and prompts for missing parameters (InitialSize, MaximumSize, Credential)
2. Establishes a PowerShell remoting session to the target computer
3. Deploys helper scripts and worker functions to the remote session
4. Executes the page file configuration remotely
5. Prompts for reboot to apply changes
6. Cleans up the remoting session

### Related Files

- Worker script: `Workers\\Set-PageFileSize.worker.ps1`
- Helper function: `Private\\PageFile\\Invoke-PageFileConfig.ps1`

### See Also

- Documentation: https://dan-damit.github.io/TechToolbox-Docs/Set-PageFileSize