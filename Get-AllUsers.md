# Get-AllUsers

Enumerates user accounts known to a computer. Returns a merged view of local user accounts, user profiles present on the machine, and users currently logged on.

---

## Overview

`Get-AllUsers` provides a comprehensive list of users associated with one or more computers. It supports both local execution and remote execution via PowerShell Remoting (WSMan) or SSH.

### Local Execution
When targeting the local computer, the function runs directly without establishing a remote session.

### Remote Execution
For remote targets, it utilizes `Start-NewPSRemoteSession` to establish a connection and `Invoke-RemoteWorker` to execute the enumeration logic. It supports:
- WSMan (Windows PowerShell Management)
- SSH transport
- CredSSP authentication

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **ComputerName** | `string[]` | One or more computer names. Defaults to the local computer (`$env:COMPUTERNAME`). Accepts pipeline input. Aliases: `Name`, `CN`. |
| **Credential** | `PSCredential` | Credential used for remote session creation when targeting other machines. |
| **UseSsh** | `switch` | Use SSH transport instead of WSMan for remote execution. |
| **UseCredSSP** | `switch` | Use CredSSP authentication for WSMan remoting. |
| **Port** | `int` | The port to use when `-UseSsh` is specified. Default: 22. |
| **Ps7ConfigName** | `string` | The WSMan PowerShell 7 endpoint name. Default: `'PowerShell.7'`. |
| **WinPsConfigName** | `string` | The WSMan Windows PowerShell endpoint name. Default: `'Microsoft.PowerShell'`. |
| **UserName** | `string` | SSH username when not using PSCredential for authentication. |
| **KeyFilePath** | `string` | Path to the SSH private key file when using key-based authentication. |

---

## How It Works

### 1. Target Resolution
Determines if the target is local or remote based on `$ComputerName`. If multiple computers are specified, it iterates through each.

### 2. Remote Session Establishment (Remote Targets)
If a remote computer is targeted:
- Establishes a PSRemoting session using `Start-NewPSRemoteSession`.
- Configures the session based on `-UseSsh`, `-UseCredSSP`, and port settings.
- Passes credentials or key paths as required by the transport method.

### 3. User Enumeration (Worker)
The worker logic merges data from three sources:
1. **Local User Accounts**: Retrieved via standard AD/Local SAM queries.
2. **User Profiles**: Profiles present on the file system (`C:\Users`).
3. **Logged-On Users**: Currently active sessions detected via WMI/CIM or PSRemoting commands.

### 4. Output
Returns a unified collection of user objects containing identity information, profile paths, and login status.

---

## Examples

### **Enumerate local users**
```powershell
Get-AllUsers
```

### **Enumerate remote computer using default credentials**
```powershell
Get-AllUsers -ComputerName "SRV01"
```

### **Enumerate multiple computers via SSH**
```powershell
Get-AllUsers -ComputerName "SRV01", "SRV02" -UseSsh -UserName "admin" -KeyFilePath "C:\keys\id_rsa"
```

### **Enumerate remote computer using CredSSP**
```powershell
$cred = Get-Credential
Get-AllUsers -ComputerName "SRV01" -Credential $cred -UseCredSSP
```

---

## Notes

- Requires appropriate permissions on the target computers.
- For remote execution, PowerShell Remoting must be enabled and configured correctly.
- SSH transport requires OpenSSH client/server components to be installed.
- CredSSP delegation must be configured in both local and remote Group Policy if used.