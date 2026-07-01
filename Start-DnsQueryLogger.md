# Start-DnsQueryLogger

## SYNOPSIS
Starts real-time DNS query logging using the Windows DNS debug log.

## SYNTAX

```powershell
Start-DnsQueryLogger [[-WhatIf] [-Confirm]] [<CommonParameters>]
```

## DESCRIPTION
The `Start-DnsQueryLogger` cmdlet starts logging DNS queries by enabling the Windows DNS debug log. It reads configuration settings from the TechToolbox config.json file to determine if DNS logging is enabled, the log file path, and parsing mode.

If logging is enabled, it ensures the log directory exists and starts the DNS query logger. This function relies on internal TechToolbox runtime initialization (`Initialize-TechToolboxRuntime`) to load configuration settings.

## EXAMPLES

### Example 1
```powershell
Start-DnsQueryLogger
```
**Description:**
Starts the DNS query logger based on the configuration settings found in `config.json`.

## PARAMETERS

### -WhatIf
Describes what would happen if you executed the command without actually executing it. For more information, see [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

- **Type:** System.Management.Automation.SwitchParameter
- **Position:** Named
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

### -Confirm
Prompts you for confirmation before executing the command. For more information, see [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

- **Type:** System.Management.Automation.SwitchParameter
- **Position:** Named
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

### CommonParameters
This cmdlet supports the common parameters: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction, and -WarningVariable. For more information, see [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

## INPUTS
None. You cannot pipe objects to Start-DnsQueryLogger.

## OUTPUTS
None. Output is written to the Information stream.

## NOTES
- This function requires TechToolbox runtime initialization.
- DNS logging must be enabled in `config.json` for this command to perform any action.

## LINK
[TechToolbox](https://github.com/dan-damit/TechToolbox)
