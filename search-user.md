# Search-User

Searches Active Directory for a user using a two‑phase lookup strategy and returns a fully normalized identity record.  
This is TechToolbox’s primary identity‑resolution function and is used internally by many other commands.

---

## Overview

`Search-User` performs a robust, multi‑stage lookup against Active Directory:

### **Phase 1 — Exact Match**
Attempts a strict lookup using:
- `sAMAccountName` (if no `@` in input)
- `userPrincipalName` (if input resembles a UPN)

All values are **RFC 4515‑escaped** to prevent LDAP injection.

### **Phase 2 — Broad Fallback**
If Phase 1 returns no results, a wide OR‑filter is constructed across:
- `sAMAccountName`
- `userPrincipalName`
- `displayName` (wildcard)
- `mail` (optional)
- `proxyAddresses` (optional, both `SMTP:` and `smtp:`)

Fallback attributes can be individually disabled.

### **Normalization**
All matched AD objects are passed through `Format-UserRecord`, which:
- Normalizes identity fields
- Optionally resolves the user’s manager into a structured object
- Optionally expands group membership into structured group objects

### **Multiplicity Handling**
- Default: **throws** if more than one match is found  
- With `-AllowMultiple`: returns **all** matches as an array

---

## Requirements

- ActiveDirectory module (RSAT or AD DS role)
- TechToolbox runtime initialized
- AD configuration present in `settings.ad` and `settings.userSearch`

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **Identity** | `string` | Required. SAM, UPN, display name, or SMTP/proxy address. |
| **Server** | `string` | Domain controller to target. Overrides config. |
| **SearchBase** | `string` | OU/container DN to scope the search. Overrides config. |
| **SearchScope** | `string` (`Base`, `OneLevel`, `Subtree`) | LDAP search scope. Overrides config. |
| **Credential** | `PSCredential` | Used for all AD operations. |
| **EnableProxyAddressSearch** | `switch` | Include proxyAddresses in fallback search. Default: **on**. |
| **EnableMailSearch** | `switch` | Include `mail` attribute in fallback search. Default: **on**. |
| **ResolveManager** | `switch` | Resolve manager into structured object. Default: **on**. |
| **ResolveGroups** | `switch` | Expand group membership. Default: **on**. |
| **AllowMultiple** | `switch` | Return all matches instead of throwing on ambiguity. |

---

## How It Works

### **1. Configuration Loading**
Pulls defaults from:

```
settings.ad
settings.userSearch
```

These control:
- Default DC
- Default SearchBase
- Default SearchScope
- AD property sets for normalization

### **2. RFC 4515 Escaping**
User input is sanitized to prevent LDAP filter injection:

```
\  *  (  )  NUL
```

are replaced with their hex‑escaped equivalents.

### **3. Exact Match Query**
Depending on input:

```text
(sAMAccountName=jdoe)
(userPrincipalName=jdoe@company.com)
```

### **4. Fallback Query**
If no exact match:

```text
(|(sAMAccountName=jdoe)(userPrincipalName=jdoe)(displayName=*jdoe*)(mail=jdoe)(proxyAddresses=SMTP:jdoe)(proxyAddresses=smtp:jdoe))
```

### **5. Normalization**
Each AD object is passed to:

```
Format-UserRecord
```

which returns a unified identity object with:
- UPN
- SAM
- DisplayName
- Mail
- Manager (optional)
- Groups (optional)
- DN
- Domain
- Additional AD attributes defined in config

### **6. Multiplicity Handling**
- Default: throws if >1 match  
- With `-AllowMultiple`: returns an array of normalized records

---

## Examples

### **Basic lookup**
```powershell
Search-User -Identity "jdoe"
```

### **UPN lookup**
```powershell
Search-User -Identity "jdoe@company.com"
```

### **Display name wildcard search**
```powershell
Search-User -Identity "John Doe"
```

### **Fast lookup (no manager or groups)**
```powershell
Search-User -Identity "jdoe" -ResolveManager:$false -ResolveGroups:$false
```

### **Target a specific DC and OU**
```powershell
Search-User -Identity "jdoe" -Server "dc01.company.com" -SearchBase "OU=Users,DC=company,DC=com"
```

### **Return all matches**
```powershell
Search-User -Identity "jdoe" -AllowMultiple
```

### **Restrict fallback attributes**
```powershell
Search-User -Identity "jdoe" -EnableProxyAddressSearch:$false -EnableMailSearch:$false
```

---

## Notes

- Requires the **ActiveDirectory** module.
- AD PSDrive is removed after import to suppress noise.
- LDAP filters are fully RFC 4515‑escaped.
- `-IncludeEXO` and `-IncludeTeams` exist but are **not implemented yet**.
- `$ErrorActionPreference` is temporarily set to `Stop` and restored afterward.
