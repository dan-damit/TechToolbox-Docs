# Get-BatteryHealth

Generates a Windows battery report and parses its HTML into structured JSON with health metrics.

---

## Overview

`Get-BatteryHealth` runs the `powercfg /batteryreport` command to produce an HTML battery report. It then parses the "Installed batteries" table from that report, computes health metrics (Full Charge Capacity vs Design Capacity ratios), logs progress, and exports a JSON file containing the parsed data.

Paths for the output files can be provided via parameters or taken from the TechToolbox configuration under the `BatteryReport` section.

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **ReportPath** | `string` | Output path for the HTML report (e.g., `C:\Temp\battery-report.html`). If omitted, uses config. |
| **OutputJson** | `string` | Path to write parsed JSON (e.g., `C:\Temp\installed-batteries.json`). If omitted, uses config. |
| **DebugInfo** | `string` | Optional path to write parser debug info (e.g., detected headings) when table detection fails. If omitted, uses config. |

---

## How It Works

### 1. Report Generation
Executes `powercfg /batteryreport` to generate the standard Windows battery health HTML report.

### 2. Parsing and Calculation
Parses the "Installed batteries" table from the generated HTML. Calculates health metrics by comparing Full Charge Capacity against Design Capacity for each cell/battery.

### 3. Output Export
Exports the parsed data as a JSON file containing detailed battery information and health percentages.

---

## Examples

### **Generate report with default paths**
```powershell
Get-BatteryHealth
```

### **Specify custom output paths**
```powershell
Get-BatteryHealth -ReportPath 'C:\Temp\battery-report.html' -OutputJson 'C:\Temp\batteries.json'
```

### **Preview file creation without writing**
```powershell
Get-BatteryHealth -ReportPath 'C:\Temp\battery-report.html' -OutputJson 'C:\Temp\batteries.json' -WhatIf
```

---

## Notes

- Requires appropriate permissions to run `powercfg`.
- The function supports `-WhatIf` and `-Confirm` via `[CmdletBinding]`.
- Debug info can be useful if the HTML structure changes and parsing fails.
END_TOOL_FILE