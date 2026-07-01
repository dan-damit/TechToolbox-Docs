# Get-InstalledPrinters

Enumerates printers installed for the currently logged-on user on one or more remote computers.

---

## Overview

`Get-InstalledPrinters` queries remote machines to identify printers visible to the active interactive user. It combines data from the registry (user-specific printer connections) and WMI/CIM (`Win32_Printer`) to provide a comprehensive view of both machine-level and per-user printer mappings.

### Local Execution
When targeting the local computer, the function runs directly without establishing a remote session.

### Remote Execution
For remote targets, it utilizes `Start-NewPSRemoteSession` to establish a connection. It supports:
- WSMan (Windows PowerShell Management)
- SSH transport
- CredSSP authentication

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **ComputerName** | `string[]` | One or more remote computer names. Mandatory. Accepts pipeline input. Aliases: `Name`, `CN`. |
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

### 3. Printer Enumeration (Worker)
The worker logic performs the following steps:
1. **Detect Active User**: Identifies the currently logged-on interactive user on the target host.
2. **Registry Query**: Reads the `HKEY_USERS` registry hive for the active user to find per-user printer connections (`HKCU\Software\Microsoft\Windows NT\CurrentVersion\PrinterPorts`).
3. **WMI Query**: Retrieves all printers from `Win32_Printer` on the target.
4. **Correlation**: Merges the registry data with WMI output to distinguish between machine-level and user-specific mappings.

### 4. Output
Returns a collection of printer objects containing identity information, connection status, and mapping type (machine vs. user).

---

## Examples

### **Get printers for local computer**
```powershell
Get-InstalledPrinters -ComputerName $env:COMPUTERNAME
```

### **Get printers for a remote computer using default credentials**
```powershell
Get-InstalledPrinters -ComputerName "SRV01"
```

### **Get printers for multiple computers via SSH**
```powershell
Get-InstalledPrinters -ComputerName "SRV01", "SRV02" -UseSsh -UserName "admin" -KeyFilePath "C:\keys\id_rsa"
```

### **Get printers for a remote computer using CredSSP**
```powershell
$cred = Get-Credential
Get-InstalledPrinters -ComputerName "SRV01" -Credential $cred -UseCredSSP
```

---

## Notes

- Requires appropriate permissions on the target computers.
- For remote execution, PowerShell Remoting must be enabled and configured correctly.
- SSH transport requires OpenSSH client/server components to be installed.