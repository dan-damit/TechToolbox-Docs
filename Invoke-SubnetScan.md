# Invoke-SubnetScan

## SYNOPSIS
Performs a subnet scan to discover hosts and gather network information.

## SYNTAX

### CIDR (Default)
```powershell
Invoke-SubnetScan [-CIDR] <String> [[-ComputerName] <String>] [-Transport <String>] [-Credential <PSCredential>] [-UserName <String>] [-KeyFilePath <String>] [-LocalOnly] [-Port <Int32>] [-ResolveNames] [-HttpBanner] [<CommonParameters>]
```

## DESCRIPTION
The `Invoke-SubnetScan` function scans a subnet (specified in CIDR notation) to identify active hosts, retrieve MAC addresses, resolve DNS names, check TCP ports, and optionally fetch HTTP banner information. It can execute locally or on a remote computer via WSMan or SSH.

### Workflow Summary
- Initializes TechToolbox runtime.
- Validates the provided CIDR subnet.
- If `-ComputerName` is specified (or defaults to local), it determines the execution context.
- For remote targets, it establishes a PowerShell session using `Start-NewPSRemoteSession` with the specified transport (`WSMan` or `SSH`) and credentials.
- Executes the scan logic within the target context:
  - Pings hosts in the subnet to check availability.
  - Retrieves MAC addresses for active hosts.
  - Optionally resolves hostnames via DNS, NetBIOS, and mDNS if `-ResolveNames` is used.
  - Checks a specific TCP port (default from config) on each host if `-Port` is specified.
  - Fetches HTTP banners if `-HttpBanner` is used.
- Cleans up any established remote sessions in finally blocks to ensure resources are released.
- Logs per-target progress and errors using `Write-Log`.

The function supports standard PowerShell confirmation behavior via `-WhatIf` and `-Confirm`. If a WhatIf or Confirm action blocks the scan for a specific target, that target is skipped while others may continue processing.

## PARAMETERS

### -CIDR
Specifies the subnet to scan in CIDR notation (e.g., '192.168.1.0/24'). This parameter is mandatory.

*   **Type:** System.String
*   **Required?** true
*   **Position?** 1
*   **Default value:** None
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -ComputerName
Specifies the remote computer on which to execute the scan. If not specified, the scan runs locally.

*   **Type:** System.String
*   **Required?** false
*   **Position?** 2
*   **Default value:** Localhost
*   **Accept pipeline input?** true (ByValue)
*   **Accept wildcard characters?** false

### -Transport
Specifies the protocol for remote execution. Valid values are 'WSMan' or 'SSH'. Default is 'WSMan'.

*   **Type:** System.String
*   **Required?** false
*   **Position?** named
*   **Default value:** WSMan
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -Credential
Specifies a user account that has permission to perform this action. The default is the current user.

*   **Type:** System.Management.Automation.PSCredential
*   **Required?** false
*   **Position?** named
*   **Default value:** Current User
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -UserName
Specifies the username for SSH authentication. Used when `-Transport` is 'SSH'.

*   **Type:** System.String
*   **Required?** false
*   **Position?** named
*   **Default value:** None
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -KeyFilePath
Specifies the path to an SSH private key file. Used when `-Transport` is 'SSH'.

*   **Type:** System.String
*   **Required?** false
*   **Position?** named
*   **Default value:** None
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -LocalOnly
Forces local execution even if `-ComputerName` is specified. Useful for testing or when remote access is not available.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -Port
Specifies the TCP port to check for availability on each host. Default is read from TechToolbox config.

*   **Type:** System.Int32
*   **Required?** false
*   **Position?** named
*   **Default value:** Configured Default Port
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -ResolveNames
If specified, attempts to resolve hostnames for discovered hosts via DNS, NetBIOS, and mDNS.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

### -HttpBanner
If specified, fetches HTTP banner information from discovered hosts on the specified port.

*   **Type:** System.Management.Automation.SwitchParameter
*   **Required?** false
*   **Position?** named
*   **Default value:** False
*   **Accept pipeline input?** false
*   **Accept wildcard characters?** false

## INPUTS
System.String. You can pipe a computer name to `Invoke-SubnetScan`.

## OUTPUTS
None. This function does not return any output by default, but it logs progress and errors.

## NOTES
- The function relies on the TechToolbox runtime being initialized correctly.
- Remote operations require appropriate permissions and firewall rules (WinRM or SSH) to be configured on the target machines.
- Ensure that the CIDR notation provided is valid for the target network.
- Scanning large subnets may take significant time; consider using smaller scopes if performance is a concern.

## EXAMPLES

### Example 1: Scan local subnet
```powershell
Invoke-SubnetScan -CIDR "192.168.1.0/24"
```
This command scans the local 192.168.1.x subnet for active hosts.

### Example 2: Scan remote subnet with SSH transport
```powershell
Invoke-SubnetScan -CIDR "10.0.0.0/24" -ComputerName "ScannerHost" -Transport "SSH" -UserName "admin" -KeyFilePath "C:\keys\id_rsa"
```
This command scans the 10.0.0.x subnet from a remote host using SSH authentication.

### Example 3: Scan with hostname resolution and port check
```powershell
Invoke-SubnetScan -CIDR "172.16.0.0/24" -ResolveNames -Port 80
```
This command scans the subnet, resolves hostnames for active hosts, and checks if port 80 is open on each.
