# Get-SystemTrustDiagnostic

## SYNOPSIS
Collects health and configuration information relevant to system trust on Windows endpoints.

## SYNTAX

```powershell
Get-SystemTrustDiagnostic [[-ComputerName] <String[]>]
    [-Credential <PSCredential>]
    [-UseSsh]
    [-Port <Int32>]
    [-Ps7ConfigName <String>]
    [-WinPsConfigName <String>]
    [-UserName <String>]
    [-KeyFilePath <String>]
    [<CommonParameters>]
```

## DESCRIPTION
The `Get-SystemTrustDiagnostic` function collects health and configuration information relevant to system trust on Windows endpoints. It queries various components such as Secure Boot, TPM (Trusted Platform Module), and general Windows Trust settings.

This cmdlet supports remoting via WSMan or SSH transport. When targeting remote computers, it can use either PowerShell 7 or Windows PowerShell remoting configurations based on the specified parameters.

## PARAMETERS

### -ComputerName <String[]>
The target computer(s) to query. Defaults to the local computer if not specified.

- **Required?** false
- **Default Value:** $env:COMPUTERNAME
- **Accept Pipeline Input?** true (ByValue)

### -Credential <PSCredential>
Credential used for WSMan/SSH remoting (optional).

- **Required?** false
- **Default Value:** None
- **Accept Pipeline Input?** false

### -UseSsh <SwitchParameter>
Use SSH transport instead of WSMan.

- **Required?** false
- **Default Value:** False
- **Accept Pipeline Input?** false

### -Port <Int32>
SSH port (default 22).

- **Required?** false
- **Default Value:** 22
- **Accept Pipeline Input?** false

### -Ps7ConfigName <String>
WSMan endpoint name for PS7 (default 'PowerShell.7').

- **Required?** false
- **Default Value:** PowerShell.7
- **Accept Pipeline Input?** false

### -WinPsConfigName <String>
WSMan endpoint name for Windows PowerShell (default 'Microsoft.PowerShell').

- **Required?** false
- **Default Value:** Microsoft.PowerShell
- **Accept Pipeline Input?** false

### -UserName <String>
SSH username (optional if Credential provided).

- **Required?** false
- **Default Value:** None
- **Accept Pipeline Input?** false

### -KeyFilePath <String>
SSH key path for key-based auth.

- **Required?** false
- **Default Value:** None
- **Accept Pipeline Input?** false

## INPUTS
[String]
You can pipe a string representing the computer name to this cmdlet.

## OUTPUTS
[PSCustomObject]
The function returns a custom object with properties for each trust section (Secure Boot, TPM, Windows Trust, and general System info). Each section includes a health state, condition description, and relevant evidence.

## NOTES
This command is part of the TechToolbox module suite designed for system administration and diagnostics.

## EXAMPLES

### Example 1: Local Computer
```powershell
Get-SystemTrustDiagnostic
```
Description:
Collects trust diagnostic information from the local computer using default settings.

### Example 2: Remote Computer via WSMan
```powershell
$cred = Get-Credential
Get-SystemTrustDiagnostic -ComputerName "RemotePC01" -Credential $cred
```
Description:
Collects trust diagnostic information from a remote computer named 'RemotePC01' using the provided credentials.

### Example 3: Remote Computer via SSH
```powershell
Get-SystemTrustDiagnostic -ComputerName "RemotePC02" -UseSsh -UserName "admin" -KeyFilePath "C:\keys\id_rsa"
```
Description:
Collects trust diagnostic information from a remote computer named 'RemotePC02' using SSH transport with key-based authentication.