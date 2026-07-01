# Set-ProxyAddress

## SYNOPSIS
Sets the primary SMTP proxy address for an Active Directory user.

## SYNTAX

```powershell
Set-ProxyAddress [-Username] <String> [-ProxyAddress] <String> [<CommonParameters>]
```

## DESCRIPTION
The `Set-ProxyAddress` function sets the primary SMTP proxy address for a specified Active Directory user. It ensures that the new primary address is added correctly and removes any existing primary SMTP addresses.

This command does not accept piped input.

## EXAMPLES

### Example 1: Set Primary SMTP Address
```powershell
Set-ProxyAddress -Username "jdoe" -ProxyAddress "jdoe@example.com"
```

**Description:**
Sets the primary SMTP proxy address for the user 'jdoe' to 'jdoe@example.com'.

## PARAMETERS

### -ProxyAddress
The new primary SMTP proxy address to set (e.g., user@example.com).

```yaml
  Required:                    true
  Position:                   2
  Default value:
  Accept pipeline input?      False
  Accept wildcard characters? false
```

### -Username
The username (sAMAccountName) of the Active Directory user.

```yaml
  Required:                    true
  Position:                   1
  Default value:
  Accept pipeline input?      False
  Accept wildcard characters? false
```

## INPUTS
None. You cannot pipe objects to Set-ProxyAddress.

## OUTPUTS
None. Output is written to the Information stream.

## NOTES
This function relies on Active Directory cmdlets. Ensure the ActiveDirectory module is available in the session.

## LINKS
[TechToolbox](https://github.com/dan-damit/TechToolbox)
