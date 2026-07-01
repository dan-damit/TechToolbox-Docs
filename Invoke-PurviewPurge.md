# Invoke-PurviewPurge

## SYNOPSIS
Executes an end-to-end Purview mailbox HardDelete purge workflow.

## SYNTAX
```powershell
Invoke-PurviewPurge [-UserPrincipalName] <String> [-Ticket] <String> [[-ContentMatchQuery] <String>] [[-Log] <Hashtable>] [-ShowProgress] [<CommonParameters>]
```

## DESCRIPTION
Invoke-PurviewPurge orchestrates a full content purge workflow against Microsoft Purview Compliance Search using the fixed case name "Content Search".

The function performs the following steps:
1. Initializes TechToolbox runtime/config and logging.
2. Normalizes and validates the ticket as `#INC-<integer>`.
3. Optionally prompts to confirm/correct ticket input if interactive mode is enabled.
4. Connects to Purview (SearchOnly session via Exchange Online module).
5. Reuses an existing search query when safe, or prompts for a new query.
6. Lints ContentMatchQuery and blocks continuation until valid.
7. Ensures mailbox-only Compliance Search exists/updates by ticket name.
8. Waits for search object registration (when newly created).
9. Starts the search when required and waits for completion.
10. Submits a HardDelete purge when matching mailbox items are found.

The function supports ShouldProcess (`-WhatIf`/`-Confirm`) for start and purge actions. If WhatIf/Confirm prevents actionable steps, execution exits safely with logs.

### Interactive Behavior
- When prompting is enabled by config, missing/invalid ticket and query values are requested interactively.
- Enter `q`, `quit`, or `exit` at prompts to cancel.

### Defaults
Default timeout/poll values are sourced from config and fall back to:
- Search completion timeout: 2400 seconds
- Search completion poll: 20 seconds
- Registration timeout: 90 seconds
- Registration poll: 3 seconds

## PARAMETERS

### -UserPrincipalName <String>
The UPN used to connect to Purview/Exchange Online (for example, analyst@company.com).

| Attribute | Value |
| :--- | :--- |
| Required? | true |
| Position? | 1 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Ticket <String>
The ticket number associated with the purge request. Must be in the format `#INC-<integer>`.

| Attribute | Value |
| :--- | :--- |
| Required? | true |
| Position? | 2 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -ContentMatchQuery <String>
The content match query string for the Compliance Search. If not provided, it may be prompted interactively.

| Attribute | Value |
| :--- | :--- |
| Required? | false |
| Position? | 3 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -Log <Hashtable>
A hashtable for logging configuration.

| Attribute | Value |
| :--- | :--- |
| Required? | false |
| Position? | 4 |
| Default value | None |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

### -ShowProgress <Switch>
Indicates that this cmdlet supports the ShouldProcess feature. When present, it shows progress during execution.

| Attribute | Value |
| :--- | :--- |
| Required? | false |
| Position? | named |
| Default value | False |
| Accept pipeline input? | false |
| Accept wildcard characters? | false |

## INPUTS
None. You cannot pipe objects to Invoke-PurviewPurge.

## OUTPUTS
None. Output is written to the Information stream.

## NOTES
- The function relies on TechToolbox configuration for default timeouts and polling intervals.
- Interactive prompts are controlled by config settings.
- Ensure you have appropriate permissions in Microsoft Purview/Exchange Online before running this command.

## EXAMPLES
### Example 1: Basic Purge
```powershell
Invoke-PurviewPurge -UserPrincipalName analyst@company.com -Ticket "#INC-123456" -ContentMatchQuery 'From:user@example.com'
```
**Description:**
Executes a purge for ticket INC-123456 using the specified content match query.

### Example 2: With Progress Display
```powershell
Invoke-PurviewPurge -UserPrincipalName analyst@company.com -Ticket "#INC-789012" -ShowProgress
```
**Description:**
Executes a purge for ticket INC-789012 and displays progress updates.
