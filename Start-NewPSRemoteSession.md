# Start-NewPSRemoteSession

## SYNOPSIS
Create a remote PSSession, preferring PowerShell 7 endpoint (WSMan) with fallback to Windows PowerShell. Also supports SSH transport.

## SYNTAX

### WSMan (Default)
```powershell
Start-NewPSRemoteSession [-ComputerName] <String> [[-Credential] <PSCredential>] [[-SessionName] <String>] [-UseCredSSP] [-Port <Int32>] [-Ps7ConfigName <String>] [-WinPsConfigName <String>] [-UserName <String>] [-KeyFilePath <String>] [-ConnectTimeoutSec <Int32>] [-IdleTimeoutSec <Int32>] [<CommonParameters>]
```

### SSH
```powershell
Start-NewPSRemoteSession [-ComputerName] <String> [[-Credential] <PSCredential>] [[-SessionName] <String>] -UseSsh [-Port <Int32>] [-Ps7ConfigName <String>] [-WinPsConfigName <String>] [-UserName <String>] [-KeyFilePath <String>] [-ConnectTimeoutSec <Int32>] [-IdleTimeoutSec <Int32>] [<CommonParameters>]
```

## DESCRIPTION
The `Start-NewPSRemoteSession` function creates a remote PowerShell session (PSSession) to a specified computer. It is designed to be robust and flexible, supporting both WSMan and SSH transports.

- **WSMan Transport**: By default, the function attempts to connect using the PowerShell 7 endpoint (`PowerShell.7`). If that fails or is unavailable, it falls back to the Windows PowerShell endpoint (`Microsoft.PowerShell`).
- **SSH Transport**: When `-UseSsh` is specified, the function uses `New-PSSession -HostName`, which requires PowerShell 7+ locally. It supports authentication via username/password (via Credential) or SSH keys.
- **CredSSP Support**: The `-UseCredSSP` switch enables CredSSP for WSMan remoting. It also automatically bootstraps the remote host's WSMan CredSSP server setting to allow delegated credentials if needed.
- **Timeouts**: Sensible default timeouts are applied (`ConnectTimeoutSec` and `IdleTimeoutSec`).

The function returns a live PSSession object on success, or throws an exception if the connection cannot be established.

## PARAMETERS

### -ComputerName <String>
The target computer to connect to. This can be a DNS name or an IP address.
- **Required?** true
- **Position?** 1
- **Default value:** None
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -Credential <PSCredential>
PSCredential object for authentication. Used for both WSMan and SSH transports. For SSH with key-based auth, the username can be derived from this credential or specified via `-UserName`.
- **Required?** false
- **Position?** 2
- **Default value:** None
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -SessionName <String>
The name to assign to the new PSSession. If not provided, a default name is generated.
- **Required?** false
- **Position?** 3
- **Default value:** None
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -UseSsh <Switch>
Forces the use of SSH transport instead of WSMan. Requires PowerShell 7+ on the local machine.
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -UseCredSSP <Switch>
Enables CredSSP for WSMan remoting. It also bootstraps the remote host's WSMan CredSSP server configuration to allow credential delegation.
- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -Port <Int32>
The port number for the connection. Default is 22 (SSH) or 5985/5986 (WSMan, handled internally).
- **Required?** false
- **Position?** named
- **Default value:** 22
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -Ps7ConfigName <String>
The WSMan configuration name to try first for PowerShell 7. Default is 'PowerShell.7'.
- **Required?** false
- **Position?** named
- **Default value:** PowerShell.7
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -WinPsConfigName <String>
The WSMan configuration name to fall back to for Windows PowerShell. Default is 'Microsoft.PowerShell'.
- **Required?** false
- **Position?** named
- **Default value:** Microsoft.PowerShell
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -UserName <String>
The username for SSH authentication when not using a PSCredential object. Can also be used to override the username from Credential.
- **Required?** false
- **Position?** named
- **Default value:** None
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -KeyFilePath <String>
The path to an SSH private key file for authentication.
- **Required?** false
- **Position?** named
- **Default value:** None
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -ConnectTimeoutSec <Int32>
The timeout in seconds for establishing the connection. Default is 20 seconds.
- **Required?** false
- **Position?** named
- **Default value:** 20
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -IdleTimeoutSec <Int32>
The idle timeout in milliseconds for the session. Default is 1800000 (30 minutes).
- **Required?** false
- **Position?** named
- **Default value:** 1800000
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

## INPUTS
None. You cannot pipe objects to this function.

## OUTPUTS
PSSession. The function returns a live PSSession object on success.

## NOTES
This command is part of the TechToolbox module.

## EXAMPLES

### Example 1: Connect using WSMan with default settings
```powershell
Start-NewPSRemoteSession -ComputerName 'Server01'
```
Creates a PSSession to Server01 using the PowerShell 7 endpoint, falling back to Windows PowerShell if necessary.

### Example 2: Connect using SSH with username and password
```powershell
$cred = Get-Credential
Start-NewPSRemoteSession -ComputerName 'Server01' -Credential $cred -UseSsh
```
Creates an SSH PSSession to Server01 using the provided credentials.

### Example 3: Connect using WSMan with CredSSP delegation
```powershell
Start-NewPSRemoteSession -ComputerName 'Server01' -UseCredSSP
```
Enables CredSSP for delegated authentication on the remote host and creates a PSSession.

### Example 4: Connect using SSH with key-based auth
```powershell
Start-NewPSRemoteSession -ComputerName 'Server01' -UserName 'admin' -KeyFilePath 'C:\keys\id_rsa' -UseSsh
```
Creates an SSH PSSession to Server01 using the specified private key file.
