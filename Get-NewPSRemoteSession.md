# Get-NewPSRemoteSession

## Purpose

`Get-NewPSRemoteSession` lists the PSSessions available in the current PowerShell runspace, selects the first session returned by `Get-PSSession`, stores it in `$script:ActivePSSession`, and returns that session. The selected variable is intended for use by related scripts such as `Start-NewPSRemoteSession` and `Stop-PSRemoteSession`.

## Usage

```powershell
Get-NewPSRemoteSession
```

The function has no parameters.

## Behavior

- Calls `Get-PSSession` and collects its results.
- If there are no sessions, writes a warning-level log message and returns without a session value.
- If sessions are available, builds a display list with `Id`, `Name`, `ComputerName`, `ConfigurationName`, `State`, and `Availability`.
- Displays that list as an auto-sized table using `Format-Table` and `Out-Host`.
- Selects the first session from the `Get-PSSession` results, assigns it to `$script:ActivePSSession`, logs the selected session's name and ID, and returns the session object.

The function does not accept a session name or ID; it always selects the first session returned by `Get-PSSession`.

## Output

When a session is found, the function returns the selected PSSession object. The displayed table contains these properties:

| Property | Description |
| --- | --- |
| `Id` | Session ID |
| `Name` | Session name |
| `ComputerName` | Remote computer name |
| `ConfigurationName` | Session configuration name |
| `State` | Session state |
| `Availability` | Session availability |

The source comment's `.OUTPUTS` block also mentions `Created`, but the function's displayed objects do not include that property. The returned value is the original PSSession object, not one of the display objects.

## Examples

List available sessions, select the first, and return it:

```powershell
$session = Get-NewPSRemoteSession
```

Use the selected session variable from another script in the same script scope:

```powershell
$script:ActivePSSession
```

## Related commands

- `Start-NewPSRemoteSession`
- `Stop-PSRemoteSession`

## Source link

[Get-NewPSRemoteSession documentation](https://dan-damit.github.io/TechToolbox-Docs/Get-NewPSRemoteSession)
