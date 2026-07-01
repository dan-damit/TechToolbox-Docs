---
title: Enable-NetFx3
external help file: TechToolbox.psm1
Module Name: TechToolbox
online version:
schema: 2.0.0
---

# Enable-NetFx3

## SYNOPSIS
Enables the .NET Framework 3.5 feature on the specified computer(s).

## SYNTAX

### ComputerName (Default)
\[\-ComputerName \<String[]>\> \[\-Credential \<PSCredential>] \[\-Source \<String>] \\
        \[\-UseCredSSP] \[\-Quiet] \[\-NoRestart] \[\-TimeoutMinutes \<Int32>] \\
        \[\-Validate]\n

## DESCRIPTION
This function allows you to enable the .NET Framework 3.5 feature on local or remote computers.
It supports specifying a source path for the feature files, using CredSSP for delegated credentials,
and various other options.

.NET Framework 3.5 includes .NET Framework 2.0 and 3.0. It is often required by older applications.
This function uses `Enable-WindowsOptionalFeature` or `Install-WindowsFeature` depending on the OS
type (client vs server) to enable the feature.

## EXAMPLES
### Example 1
```powershell
# Enable .NET Framework 3.5 on the local computer
Enable-NetFx3
```
### Example 2
```powershell
# Enable .NET Framework 3.5 on a remote computer using specific credentials
$cred = Get-Credential
Enable-NetFx3 -ComputerName 'RemotePC01' -Credential $cred
```
### Example 3
```powershell
# Enable .NET Framework 3.5 from a local source (e.g., installation media)
Enable-NetFx3 -Source 'D:\sources\sxs'
```

## PARAMETERS

### -ComputerName
The name(s) of the target computer(s) where the .NET Framework 3.5 feature should be enabled.
Defaults to the local computer if not specified.

```yaml
Type: String[]
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -Credential
The credentials to use for the remote session. If not specified, the script will attempt to use stored domain admin credentials.

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Cred
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -Source
The path to the source files for the .NET Framework 3.5 feature. Can be a local path or a UNC path.
This is useful when Windows Update is not available or configured.

```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -UseCredSSP
Switch to indicate whether to use CredSSP for delegated credentials in remote sessions.
This allows the computer to pass credentials from the local computer to a remote server for authentication.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -Quiet
Switch to suppress output messages. Only errors will be displayed.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -NoRestart
Switch to prevent automatic restart after enabling the feature. If a restart is required, it will be noted but not forced.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -TimeoutMinutes
The maximum time, in minutes, to wait for the feature to be enabled. Default is 60 minutes.

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: 60
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -Validate
Switch to perform validation after enabling the feature. This checks if .NET Framework 3.5 is actually enabled.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

## INPUTS
### System.String
You can pipe computer names to this function.

## OUTPUTS
### System.Object
This function returns a custom object indicating the status of the operation.

## NOTES
- Requires administrative privileges on the target computer(s).
- If using CredSSP, ensure it is configured correctly on both client and server.
- The 'Source' parameter is optional but recommended in disconnected environments.

## RELATED LINKS