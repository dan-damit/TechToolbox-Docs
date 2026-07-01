# Test-PathAs

## SYNOPSIS
Tests whether a path exists using alternate credentials.

## SYNTAX
```powershell
Test-PathAs [-Path] <String> [-Credential] <PSCredential> [<CommonParameters>]
```

## DESCRIPTION
The `Test-PathAs` cmdlet uses the TechToolbox impersonation subsystem to evaluate whether a file system path exists under the security context of the specified credential. This is useful for validating SMB access, deployment accounts, service accounts, and cross-domain permissions.

This command does not support piping input objects directly.

## PARAMETERS

### -Path
The file system or UNC path to test.

| Property | Value |
| --- | --- |
| Required? | true |
| Position? | 1 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Credential
The credential to impersonate while testing the path.

| Property | Value |
| --- | --- |
| Required? | true |
| Position? | 2 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to Test-PathAs.

## OUTPUTS
[System.Boolean]
The cmdlet returns `$true` if the path exists, otherwise `$false`.

## NOTES
This function relies on the TechToolbox impersonation subsystem (`Invoke-Impersonation`). Ensure that the provided credential has appropriate permissions to access the target path.

## EXAMPLE 1
```powershell
# Test a UNC path using alternate credentials
$cred = Get-Credential
Test-PathAs -Path "\\server\share\installer.msi" -Credential $cred
```
This example tests whether the specified installer file exists on the remote share using the provided credential.

## EXAMPLE 2
```powershell
# Test a local restricted folder path
$svc = Get-Credential -UserName "DOMAIN\ServiceAccount"
Test-PathAs -Path "C:\RestrictedFolder" -Credential $svc
```
This example tests access to a locally restricted folder using a specific service account.

## LINKS
[TechToolbox](https://github.com/dan-damit/TechToolbox)
