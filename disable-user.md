# Disable-User

Disables an Active Directory user account and optionally performs cloud offboarding actions such as mailbox forwarding and manager access.  
This is TechToolbox’s **full offboarding workflow**, combining AD, Exchange Online, and configuration-driven automation.

---

## Overview

`Disable-User` executes a multi-step, configurable offboarding pipeline:

### **1. Identity Resolution**
Uses `Search-User` to resolve the identity into a normalized AD record.

### **2. AD Disable + OU Move**
- Disables the AD account  
- Moves the user to the configured Disabled OU (`settings.offboarding.disabledOU`)

### **3. AD Description Stamping**
Adds a dated note to the user’s AD Description field, including forwarding details when applicable.

### **4. Optional AD Group Cleanup**
If enabled in config (`cleanupADGroups`) or via parameters, removes all AD group memberships.

### **5. Optional Exchange Online Offboarding**
When `-IncludeEXO` is used (or config enables it):
- Sets mailbox forwarding  
- Grants manager full mailbox access  
- Supports override forwarding address  
- Supports forward-only mode  

### **6. Summary Output**
Returns a structured object with:
- AD disable result  
- Group cleanup result  
- Forwarding result  
- Manager access result  
- Any non-fatal errors  

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **Identity** | `string` | Required. Any identity supported by `Search-User`. |
| **IncludeEXO** | `switch` | Enables Exchange Online offboarding actions. |
| **ForwardToSmtpAddress** | `string` | Override forwarding target. Strips `smtp:` prefix automatically. |
| **DeliverToMailboxAndForward** | `switch` | Keep a local mailbox copy while forwarding. Default: **true**. |
| **ForceCloud** | `switch` | Forces EXO actions even when hybrid auto-disable is enabled. |
| **Credential** | `PSCredential` | Used for AD and cloud operations. |

---

## How It Works

### **Configuration-Driven Behavior**
Reads from:

```
settings.offboarding.includeEXO
settings.offboarding.disabledOU
settings.offboarding.cleanupADGroups
settings.offboarding.useHybridAutoDisable
```

Parameter switches override config values.

---

### **Hybrid Auto-Disable Logic**

If:

```
settings.offboarding.useHybridAutoDisable = true
```

AND `-ForceCloud` is **not** used:

→ Cloud steps are skipped  
→ Assumes AAD Connect or downstream automation will propagate the disable  

Use `-ForceCloud` to override.

---

### **Exchange Online Actions (Optional)**

When EXO actions are enabled:

- Connects via `Connect-ExchangeOnlineAlways` or native `Connect-ExchangeOnline`
- Determines forwarding target:
  - Override address (highest priority)
  - Manager UPN (fallback)
- Sets forwarding with or without local copy
- Grants manager mailbox access
- Updates AD Description with forwarding outcome

Non-fatal EXO failures are captured in the returned object.

---

### **AD Description Stamping**

Always writes a note:

```
Disabled on <date>
```

If EXO is included:

```
Disabled on <date> > Mail forwarding to <target>
```

If forwarding fails:

```
Disabled on <date> > Mail forwarding FAILED to <target>
```

---

### **Return Object**

A PSCustomObject containing keys such as:

- `ADDisable`
- `ADGroups`
- `Forwarding`
- `ManagerAccess`
- `ForwardingError`
- `ManagerAccessError`

---

## Examples

### **Basic AD-only disable**
```powershell
Disable-User -Identity "jdoe"
```

### **Disable + EXO offboarding**
```powershell
Disable-User -Identity "jdoe" -IncludeEXO
```

### **Disable + EXO with forwarding override**
```powershell
Disable-User -Identity "jdoe@company.com" -IncludeEXO -ForwardToSmtpAddress "helpdesk@company.com"
```

### **Disable + EXO forward-only mode**
```powershell
Disable-User -Identity "jdoe" -IncludeEXO -DeliverToMailboxAndForward:$false
```

### **Preview actions without making changes**
```powershell
Disable-User -Identity "jdoe" -IncludeEXO -WhatIf
```

### **Force cloud actions even in hybrid environments**
```powershell
Disable-User -Identity "jdoe" -ForceCloud -IncludeEXO
```

---

## Notes

- Requires **ActiveDirectory** module.  
- EXO actions require **ExchangeOnlineManagement** or your internal helper.  
- Disabled OU, EXO defaults, and cleanup behavior are fully configurable.  
- Non-fatal EXO failures are logged and included in the output object.  
- AD Description stamping always occurs.  
- Supports `-WhatIf` and `-Confirm` for safe previewing.
