## Get-SystemUptime

Retrieves the system uptime information from one or more remote computers.

### SYNOPSIS
Retrieves the system uptime information from one or more remote computers.

### DESCRIPTION
The `Get-SystemUptime` function uses the TechToolbox remote worker engine to run a small uptime worker script on each specified remote host. It calculates the time elapsed since the last boot of the operating system.

By default, it exports the results to CSV files in a designated output directory. You can suppress this export using the `-NoExport` switch.

### SYNTAX

#### Default (Mandatory ComputerName)
```powershell
Get-SystemUptime -ComputerName <string[]> [-Credential <pscredential>] [-OutDir <string>] [-NoExport] [<CommonParameters>]
```

### PARAMETERS

#### -ComputerName
Specifies the names of the computers to query for uptime information. This parameter is mandatory.

```powershell
-ComputerName <string[]>
Required?                    true
Position?                    named
Default value                None
Accept pipeline input?       True (ByValue)
Accept wildcard characters?  false
```

#### -Credential
Specifies a user account that has permission to perform this action. The default is the current user.

```powershell
-Credential <pscredential>
Required?                    false
Position?                    named
Default value                None
Accept pipeline input?       False
Accept wildcard characters?  false
```

#### -OutDir
Specifies the directory where the CSV export files will be saved. If not specified, a default path is resolved.

```powershell
-OutDir <string>
Required?                    false
Position?                    named
Default value                None
Accept pipeline input?       False
Accept wildcard characters?  false
```

#### -NoExport
If present, prevents the function from exporting results to CSV files.

```powershell
-NoExport
Required?                    false
Position?                    named
Default value                False
Accept pipeline input?       False
Accept wildcard characters?  false
```

### INPUTS
`string[]`. You can pipe an array of computer names to this cmdlet.

### OUTPUTS
`pscustomobject`. The function returns custom objects containing uptime details for each queried computer.

### EXAMPLES

#### Example 1: Get Uptime for Localhost
```powershell
Get-SystemUptime -ComputerName localhost
```
This command retrieves the system uptime for the local machine and exports the result to CSV by default.

#### Example 2: Get Uptime for Multiple Remote Computers
```powershell
Get-SystemUptime -ComputerName "Server01", "Server02" -Credential (Get-Credential)
```
This command retrieves the system uptime for Server01 and Server02 using alternate credentials.

#### Example 3: Get Uptime Without Exporting to CSV
```powershell
Get-SystemUptime -ComputerName localhost -NoExport
```
This command retrieves the system uptime for the local machine without creating any export files.