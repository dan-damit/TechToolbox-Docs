# Get-PDQDiagLogs

## SYNOPSIS
Collect PDQ diagnostics under SYSTEM context (local and remote), zip on target, and copy back to C:\PDQDiagLogs on the machine running this script.

## SYNTAX

### FromSession (Default)
```powershell
Get-PDQDiagLogs [-ComputerName <String[]>] [-Credential <PSCredential>] [-LocalDropPath <String>] [-TransferMode <String>] [-ExtraPaths <String[]>] [-ConnectDataPath <String>] [-UseSsh] [-SshPort <Int32>]
```

### Bytes
```powershell
Get-PDQDiagLogs -ComputerName <String[]> -Credential <PSCredential> -TransferMode Bytes [-LocalDropPath <String>] [-ExtraPaths <String[]>] [-ConnectDataPath <String>] [-UseSsh] [-SshPort <Int32>]
```

### SMB
```powershell
Get-PDQDiagLogs -ComputerName <String[]> -Credential <PSCredential> -TransferMode SMB [-LocalDropPath <String>] [-ExtraPaths <String[]>] [-ConnectDataPath <String>] [-UseSsh] [-SshPort <Int32>]
```

## DESCRIPTION
The Get-PDQDiagLogs function collects PDQ diagnostics from one or more target computers. It works both locally and remotely.

- **Local & remote**: Runs a one-time Scheduled Task as SYSTEM that performs the collection on the target machine.
- **Remoting**: Uses PS7-first remoting via the New-PSRemoteSession helper if present (with fallback included).
- **Resilient copy**: Copies files using Copy-Item then robocopy /B, plus exports Event Logs via wevtutil.
- **Output**: The collected data is zipped on the target and pulled back to the collector machine, named `PDQDiag_<Computer>_<timestamp>.zip`.

## PARAMETERS

### -ComputerName
Target computer(s). Defaults to local machine.

*   **Type:** String[]
*   **Aliases:** CN, DNSHostName, Computer
*   **Required:** False
*   **Default Value:** $env:COMPUTERNAME
*   **Pipeline Input:** True (ByPropertyName, ByValue)

### -Credential
Optional credential for remote connections. If omitted and `$Global:TTDomainCred` exists, New-PSRemoteSession helper may use it.

*   **Type:** PSCredential
*   **Required:** False
*   **Default Value:** None

### -LocalDropPath
Path on the collector to store retrieved ZIP(s).

*   **Type:** String
*   **Required:** False
*   **Default Value:** C:\PDQDiagLogs

### -TransferMode
Retrieval method for remote ZIPs.

*   **Type:** String
*   **Accepted Values:** FromSession, Bytes, SMB
*   **Required:** False
*   **Default Value:** FromSession

### -ExtraPaths
Extra file/folder paths on the target(s) to include in the collection.

*   **Type:** String[]
*   **Required:** False
*   **Default Value:** None

### -ConnectDataPath
PDQ Connect data root.

*   **Type:** String
*   **Required:** False
*   **Default Value:** $env:ProgramData\PDQ\PDQConnectAgent

### -UseSsh
Switch to enable SSH for remote connections.

*   **Type:** SwitchParameter
*   **Required:** False
*   **Default Value:** False

### -SshPort
SSH port number when using SSH mode.

*   **Type:** Int32
*   **Required:** False
*   **Default Value:** 22

## EXAMPLES

### Example 1: Collect diagnostics from local machine
```powershell
Get-PDQDiagLogs
```
**Description**: Collects PDQ diagnostics from the local computer and saves the ZIP file to C:\PDQDiagLogs.

### Example 2: Collect diagnostics from remote computers using specific credentials
```powershell
$cred = Get-Credential
Get-PDQDiagLogs -ComputerName "Server01", "Server02" -Credential $cred
```
**Description**: Collects PDQ diagnostics from Server01 and Server02 using the provided credentials.

### Example 3: Use SMB transfer mode for remote collection
```powershell
Get-PDQDiagLogs -ComputerName "RemotePC" -TransferMode SMB
```
**Description**: Collects PDQ diagnostics from RemotePC using SMB as the transfer method.

## NOTES
- This function relies on the New-PSRemoteSession helper for PS7-first remoting if available.
- Ensure that the target machines allow remote scheduled task creation and file access as required by the selected TransferMode.
