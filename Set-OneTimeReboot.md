# Set-OneTimeReboot

## SYNOPSIS
Schedules or removes a one-time reboot task locally or on remote computers.

## SYNTAX

### ComputerName (Default)
```powershell
Set-OneTimeReboot [-ComputerName <String[]>] [[-DateTime] <Nullable[DateTime]>]
   [-Credential <PSCredential>] [-At <String>] [-TaskName <String>] [-Comment <String>]
   [-SelfDelete] [-WakeToRun] [-ForceCloseApps] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### Remove
```powershell
Set-OneTimeReboot -ComputerName <String[]> -Remove [[-TaskName] <String>] [-Credential <PSCredential>]
   [-WhatIf] [-Confirm] [<CommonParameters>]
```

## DESCRIPTION
The `Set-OneTimeReboot` function creates, updates, or removes a Windows Scheduled Task that executes `shutdown.exe /r` at a specified time. It supports both local and remote scheduling via PowerShell remoting (WSMan or SSH).

### Key Features:
- **Local Scheduling**: When `-ComputerName` is omitted, the task is created on the local machine.
- **Remote Scheduling**: Supports targeting multiple remote computers.
- **Remoting Protocols**: Uses WSMan by default; can use SSH if configured via `Start-NewPSRemoteSession`.
- **Idempotent Updates**: If a scheduled task with the same name already exists, it is automatically removed and recreated with new settings.
- **Remove Mode**: Use `-Remove` to unregister an existing reboot task.
- **Configuration Defaults**: Unbound parameters can be populated from `settings.oneTimeReboot` in TechToolbox config (e.g., default `At`, `SelfDelete`, `Comment`).

### Time Resolution:
- If `-DateTime` is provided, that exact local timestamp is used.
- If `-DateTime` is omitted, the `-At` parameter (HH:mm format) determines the time. The task will be scheduled for the next occurrence of that time today or tomorrow if it has already passed.

## PARAMETERS

### -ComputerName
Target computer name(s) for remote scheduling or removal.
- **Type**: `String[]`
- **Aliases**: `CN`, `Host`, `Name`
- **Pipeline Input**: True (ByPropertyName, ValueFromPipeline)
- **Default**: Local machine if omitted.

### -DateTime
Exact local date/time to use for scheduling the reboot.
- **Type**: `Nullable[DateTime]`
- **Position**: 1
- **Note**: If provided, `-At` is ignored. Declared before `-Credential` to ensure positional binding works correctly in calls like `Set-OneTimeReboot -ComputerName PC01 '4/21/2026 1:00:00 AM'`.

### -Credential
Credentials for remote session creation (WSMan/SSH).
- **Type**: `PSCredential`
- **Note**: Also supports username-only strings for SSH key authentication via the PSRemoting helper.

### -At
Time of day to schedule the reboot in HH:mm format (24-hour clock).
- **Type**: `String`
- **Validation**: Must match pattern `^(?:[01]\d|2[0-3]):[0-5]\d$`.
- **Default**: Populated from config if available; otherwise, prompts or requires `-DateTime`.

### -TaskName
Name of the scheduled task to create or remove.
- **Type**: `String`
- **Default**: Prefix defined in config (`taskNamePrefix`) plus a unique identifier.

### -Comment
Description for the scheduled task.
- **Type**: `String`
- **Default**: Populated from config (`defaultComment`).

### -SelfDelete
Specifies that the scheduled task should be deleted after it runs successfully.
- **Type**: `SwitchParameter`
- **Default**: Populated from config if available.

### -WakeToRun
Configures the task to wake the computer before running (requires hardware support).
- **Type**: `SwitchParameter`

### -ForceCloseApps
Forces applications to close without saving data during shutdown.
- **Type**: `SwitchParameter`

### -Remove
Removes an existing scheduled reboot task instead of creating one.
- **Type**: `SwitchParameter`

### -WhatIf / -Confirm
Controls safe preview behavior for local and remote operations.
- **Type**: `CommonParameters`

## INPUTS
`String[]` (ComputerName can be piped)

## OUTPUTS
None. This function does not return any objects.

## EXAMPLES

### Example 1: Schedule a reboot locally at a specific time
```powershell
Set-OneTimeReboot -DateTime '2026-07-01 02:30:00'
```
**Description**: Schedules a one-time reboot on the local machine for July 1, 2026, at 2:30 AM.

### Example 2: Schedule a reboot remotely using time of day
```powershell
Set-OneTimeReboot -ComputerName 'SRV01', 'SRV02' -At '23:00'
```
**Description**: Schedules a reboot for servers SRV01 and SRV02 at 11:00 PM the next occurrence.

### Example 3: Remove an existing reboot task
```powershell
Set-OneTimeReboot -ComputerName 'SRV01' -Remove -TaskName 'MyRebootTask'
```
**Description**: Removes the scheduled task named 'MyRebootTask' from SRV01.

## NOTES
- Requires appropriate permissions to create/manage scheduled tasks on target machines.
- Remote scheduling relies on PowerShell remoting being enabled and accessible.
- Supports `WhatIf` and `Confirm` for safe execution preview.

## LINKS
- [Get-Help Set-OneTimeReboot](https://techtoolbox.com/docs/Set-OneTimeReboot)
- Start-NewPSRemoteSession
