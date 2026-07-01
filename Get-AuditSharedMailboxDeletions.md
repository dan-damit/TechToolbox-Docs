# Get-AuditSharedMailboxDeletions

Finds shared mailbox deletion activity within a specified time window and optionally exports results to CSV.

---

## Overview

`Get-AuditSharedMailboxDeletions` initializes the TechToolbox runtime, ensures Exchange Online connectivity, and runs the shared mailbox audit worker for a specific mailbox. It searches Unified Audit Logs (UAL) for deletion events such as SoftDelete, HardDelete, and MoveToDeletedItems.

### Key Features
- Defaults `StartDate` to 14 days ago and `EndDate` to the current time.
- Supports filtering by subject content (literal strings or regex patterns).
- Filters specific UAL operations.
- Optional CSV export capability with configurable output paths.

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **Mailbox** | `string` | **(Mandatory)** Mailbox identity to audit. Accepts alias, UPN, SMTP address, etc. |
| **StartDate** | `datetime` | Query start date. Defaults to 14 days before execution time. |
| **EndDate** | `datetime` | Query end date. Defaults to the current execution time. |
| **SubjectContains** | `string[]` | Optional case-insensitive literal subject fragments. Any match includes the record. |
| **SubjectRegex** | `string[]` | Optional regex patterns tested against message subjects. Any match includes the record. |
| **Operations** | `string[]` | UAL operations to include. Defaults to `'SoftDelete'`, `'HardDelete'`, and `'MoveToDeletedItems'`. |
| **ExportCsv** | `switch` | If set, exports results to CSV. Returns the file path unless `-PassThru` is also used. |
| **ExportPath** | `string` | Destination folder for CSV export. Defaults to the configured shared mailbox audit export path, then `$env:TEMP`. |
| **PassThru** | `switch` | When used with `-ExportCsv`, returns the record objects instead of only the exported file path. |

---

## How It Works

### 1. Initialization
The function initializes the TechToolbox runtime and ensures connectivity to Exchange Online.

### 2. Audit Query Construction
- Constructs a query for Unified Audit Logs based on the provided `Mailbox`, `StartDate`, and `EndDate`.
- Applies filters for specific operations (`Operations`) and subject criteria (`SubjectContains` or `SubjectRegex`).

### 3. Execution & Filtering
Retrieves audit records matching the criteria. Records are filtered to include only those where the specified subject conditions are met.

### 4. Output Handling
- **Default**: Returns the matched audit record objects.
- **With `-ExportCsv`**: Exports the results to a CSV file in `ExportPath`. By default, returns the path to the exported file.
- **With `-ExportCsv -PassThru`**: Exports the results and also returns the object collection for further processing.

---

## Examples

### Find deletions for a shared mailbox over the last 14 days
```powershell
Get-AuditSharedMailboxDeletions -Mailbox "shared@contoso.com"
```

### Filter by specific subject keyword
```powershell
Get-AuditSharedMailboxDeletions -Mailbox "shared@contoso.com" -SubjectContains "Invoice"
```

### Export results to CSV with custom path
```powershell
Get-AuditSharedMailboxDeletions -Mailbox "shared@contoso.com" -StartDate (Get-Date).AddDays(-30) -EndDate (Get-Date) -ExportCsv -ExportPath "C:\AuditLogs"
```

### Return objects and export to CSV
```powershell
$results = Get-AuditSharedMailboxDeletions -Mailbox "shared@contoso.com" -Operations @('HardDelete') -ExportCsv -PassThru
$results | Format-Table
```

---

## Notes

- Requires appropriate permissions to access Exchange Online Unified Audit Logs.
- The default time window covers the last 14 days; adjust `StartDate` and `EndDate` as needed for older logs.
- Regex patterns in `-SubjectRegex` should follow standard PowerShell regex syntax.
- Ensure the target directory exists if specifying a custom `-ExportPath`.