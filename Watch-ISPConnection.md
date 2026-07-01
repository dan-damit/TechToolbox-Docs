# Watch-ISPConnection

## SYNOPSIS
Monitors connectivity to a host (IP, DNS name, or URL) and logs structured results.

## SYNTAX

### Start (Default)
```powershell
Watch-ISPConnection [-Target] <String> [[-IntervalSeconds] <Int32>] [[-TimeoutSeconds] <Int32>] [[-SessionName] <String>] [<CommonParameters>]
```

### Stop
```powershell
Watch-ISPConnection -Stop [-SessionName] <String> [<CommonParameters>]
```

### Status
```powershell
Watch-ISPConnection -Status [<CommonParameters>]
```

## DESCRIPTION
The `Watch-ISPConnection` cmdlet starts a background monitoring job that pings a specified target host at regular intervals. It writes structured CSV rows to a per-session log file, allowing for ongoing analysis of connectivity health.

This function supports three main actions:
1. **Start**: Initiates a new monitoring session against a target.
2. **Stop**: Terminates an existing monitoring session by name.
3. **Status**: Displays the current state of all active or recent sessions, including job IDs, start times, and last heartbeat status.

The cmdlet automatically handles URL formatting to extract the host for pinging if a full URL is provided as the target.

## PARAMETERS

### -Target
Specifies the IP address, DNS name, or URL to monitor. If a URL is provided (e.g., `https://example.com`), it will be formatted to extract just the host portion.

- **Type**: String
- **Aliases**: FirewallIP
- **Required?**: true (in Start parameter set)
- **Position?**: 1
- **Default value**: None
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -IntervalSeconds
Specifies the number of seconds between each ping sample.

- **Type**: Int32
- **Required?**: false (in Start parameter set)
- **Position?**: 2
- **Default value**: 10
- **Valid values**: Range: 1 to 86400
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -TimeoutSeconds
Specifies the timeout in seconds for each individual ping attempt.

- **Type**: Int32
- **Required?**: false (in Start parameter set)
- **Position?**: 3
- **Default value**: 3
- **Valid values**: Range: 1 to 60
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -SessionName
Specifies a custom name for the monitoring session. If omitted, a random unique name is generated automatically.

- **Type**: String
- **Required?**: false (in Start parameter set)
- **Position?**: 4
- **Default value**: Randomly generated
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -Stop
Stops a running monitoring session. Requires the `-SessionName` parameter to identify which session to stop.

- **Type**: SwitchParameter
- **Required?**: true (in Stop parameter set)
- **Position?**: named
- **Default value**: false
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

### -Status
Displays the status of all monitoring sessions. Shows job details, start time, and last heartbeat.

- **Type**: SwitchParameter
- **Required?**: true (in Status parameter set)
- **Position?**: named
- **Default value**: false
- **Accept pipeline input?**: false
- **Accept wildcard characters?**: false

## INPUTS
None. You cannot pipe objects to this cmdlet.

## OUTPUTS
System.String. The cmdlet outputs status messages and session information as strings.

## EXAMPLES

### Example 1: Start a monitoring session
```powershell
Watch-ISPConnection -Target "8.8.8.8" -IntervalSeconds 5 -SessionName "GoogleDNS"
```
**Description:**
Starts a new monitoring session named "GoogleDNS" that pings Google's public DNS server every 5 seconds.

### Example 2: Stop a monitoring session
```powershell
Watch-ISPConnection -Stop -SessionName "GoogleDNS"
```
**Description:**
Stops the monitoring session previously started with the name "GoogleDNS".

### Example 3: Check status of all sessions
```powershell
Watch-ISPConnection -Status
```
**Description:**
Lists all active and recent monitoring sessions along with their current state and last known heartbeat time.

## NOTES
This cmdlet relies on background jobs to perform the pinging. Ensure that your PowerShell environment allows for background job execution.

## RELATED LINKS
- Get-Help Watch-ISPConnection -Online
- Stop-Job
- Start-Job