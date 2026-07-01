# Get-CUCredentialManagerContents

Returns Credential Manager entry metadata for the current user context, including remote interactive user sessions.

---

## Overview

`Get-CUCredentialManagerContents` uses `cmdkey /list` to parse and return only metadata fields (Target, Type, User, Persistence, Comment) from Windows Credential Manager entries. No secret material is read or returned.

For remote hosts, this function can query the active interactive session by creating a short-lived scheduled task in the interactive user context on the target machine.

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **ComputerName** | `string[]` | One or more computer names to query. Defaults to localhost. Aliases: Name, CN. |
| **Credential** | `pscredential` | Credential used for remoting. |
| **UseSsh** | `switch` | Use SSH transport instead of WSMan when creating remote sessions. |
| **UseCredSSP** | `switch` | Use CredSSP for WSMan remoting. |
| **Port** | `int` | SSH port when -UseSsh is specified. Defaults to 22. |
| **Ps7ConfigName** | `string` | WSMan PowerShell 7 endpoint name. Defaults to 'PowerShell.7'. |
| **WinPsConfigName** | `string` | WSMan Windows PowerShell endpoint name. Defaults to 'Microsoft.PowerShell'. |
| **UserName** | `string` | SSH username when not using PSCredential. |
| **KeyFilePath** | `string` | SSH private key path when using key-based authentication. |

---

## How It Works

### 1. Local Query
On the local machine, it executes `cmdkey /list` and parses the output to extract metadata for each stored credential entry.

### 2. Remote Query
For remote computers:
- Creates a scheduled task in the active interactive user's context on the target host.
- Executes `cmdkey /list` within that session.
- Returns the parsed metadata from the remote machine.

---

## Examples

### **Get local credential manager entries**
```powershell
Get-CUCredentialManagerContents
```

### **Get entries for a specific remote computer**
```powershell
Get-CUCredentialManagerContents -ComputerName 'RemotePC01'
```

### **Use SSH transport to query a remote host**
```powershell
Get-CUCredentialManagerContents -ComputerName 'RemotePC02' -UseSsh -UserName 'admin' -KeyFilePath 'C:\keys\id_rsa'
```

---

## Notes

- Only metadata is returned; no passwords or secrets are exposed.
- Remote queries require appropriate permissions and configured remoting (WSMan/SSH) on the target host.
- The function supports pipeline input for ComputerName.