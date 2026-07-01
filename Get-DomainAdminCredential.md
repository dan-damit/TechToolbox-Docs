# Get-DomainAdminCredential

Retrieves, prompts for, persists, or clears the module's domain admin credential.

---

## Overview

`Get-DomainAdminCredential` provides a centralized credential workflow for the module's domain admin account. It supports four primary behaviors:

1. **RETURN CACHED CREDENTIAL**: If an in-memory `PSCredential` already exists in `$script:domainAdminCred`, it is reused by default to avoid repeated prompts during the same session.
2. **REBUILD FROM STORED SETTINGS**: If no in-memory credential is available, the function attempts to reconstruct one from:
   - `settings.passwords.domainAdminCred.username` in `config.json`
   - `passwords.domainAdminCred.password` in `config.secrets.json`

   The password is stored only as DPAPI-protected SecureString text and can only be decrypted under the same Windows user / machine / security context that created it.
3. **PROMPT FOR NEW CREDENTIAL**: If no usable cached/stored credential exists, or if `-ForcePrompt` is supplied, the function prompts interactively via `Get-Credential` and stores the result in memory for the current session.
4. **PERSIST OR CLEAR STORED VALUES**:
   - `-Persist`: Writes the username to `config.json` and the DPAPI-protected password blob to `config.secrets.json`.
   - `-Clear`: Removes both stored values and clears the in-memory cache.

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **-Clear** | `switch` | Clears the stored username/password from config files and the in-memory cache. |
| **-ForcePrompt** | `switch` | Forces an interactive prompt for credentials even if cached/stored values exist. |
| **-Persist** | `switch` | Persists the current credential (username to `config.json`, password to `config.secrets.json`). |
| **-PassThru** | `switch` | Passes the resulting `PSCredential` object through to the pipeline. |

---

## Storage Model

- The **username** is treated as non-secret configuration and stored in `config.json`.
- The **password** is never written to `config.json`; it is stored only in `config.secrets.json` using `ConvertFrom-SecureString`, which relies on Windows DPAPI by default.

---

## Examples

### Retrieve cached credential or prompt if none exists
```powershell
Get-DomainAdminCredential
```

### Force interactive prompt for new credentials
```powershell
Get-DomainAdminCredential -ForcePrompt
```

### Persist current in-memory credential to config files
```powershell
Get-DomainAdminCredential -Persist
```

### Clear stored and cached credentials
```powershell
Get-DomainAdminCredential -Clear
```

---

## Notes

- The password storage relies on Windows DPAPI, meaning the persisted credential can only be decrypted by the same user/machine context that created it.
- Use `-PassThru` if you need to access the `PSCredential` object directly for further processing.