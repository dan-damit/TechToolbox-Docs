# Get-WindowsProductKey

## SYNOPSIS
Retrieves Windows product key information from a local or remote computer.

## SYNTAX
```
Get-WindowsProductKey [[-ComputerName] <String>] [-Credential <PSCredential>]
```

## DESCRIPTION
The `Get-WindowsProductKey` cmdlet retrieves the Windows product key, including OEM keys and partial keys, as well as activation status information from a specified computer. It uses WMI (Windows Management Instrumentation) and `slmgr.vbs` to gather this data.

This function supports both local and remote queries. When querying a remote computer, you must provide valid credentials via the `-Credential` parameter.

## PARAMETERS

### -ComputerName <String>
The name of the computer to query for product key information.
- **Default Value**: The local computer (`$env:COMPUTERNAME`).
- **Required?**: No
- **Accept Pipeline Input?**: No
- **Accept Wildcard Characters?**: No

### -Credential <PSCredential>
The credentials required to connect to a remote computer.
- **Required?**: No
- **Accept Pipeline Input?**: No
- **Accept Wildcard Characters?**: No

## INPUTS
None. You cannot pipe objects to `Get-WindowsProductKey`.

## OUTPUTS
The cmdlet outputs an object containing the following properties:
- **ComputerName**: The name of the computer queried.
- **OEMKey**: The original OEM product key (if available).
- **PartialKey**: The partial product key currently installed.
- **ActivationStatus**: The current Windows activation status.
- **ErrorMessage**: Any error message encountered during retrieval (null if successful).

## EXAMPLES
### Example 1: Get local product key
```powershell
Get-WindowsProductKey
```
This command retrieves the product key information for the local computer.

### Example 2: Get remote product key with credentials
```powershell
$cred = Get-Credential
Get-WindowsProductKey -ComputerName "RemotePC01" -Credential $cred
```
This command prompts for credentials and then retrieves the product key information from `RemotePC01`.

## NOTES
- Requires appropriate permissions to query WMI on remote computers.
- Logs results to a directory specified in the TechToolbox configuration (`windowsActivation.logDir`).
