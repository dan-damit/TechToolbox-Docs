# **Markdown Documentation: `Reset-ADPassword`**

```markdown
---
title: Reset-ADPassword
layout: default
nav_order: 2
parent: Commands
---

# Reset-ADPassword

Resets an Active Directory user’s password using TechToolbox’s private password generator.  
This function is designed for technicians who need a fast, safe, and consistent way to reset passwords while maintaining AD compliance and logging standards.

---

## Overview

`Reset-ADPassword` performs a full password‑reset workflow:

- Loads TechToolbox runtime and configuration
- Dot-sources the private password generator (`Get-NewPassword`)
- Generates a cryptographically strong password using your configured defaults
- Resets the AD user’s password
- Optionally:
  - Unlocks the account
  - Sets “User must change password at next logon”
  - Writes the password to a file
  - Copies the password to clipboard (Windows only)
  - Displays the password in console (if enabled in config)
- Logs all actions using `Write-Log`

This function is part of the **TechToolbox AD Toolkit**.

---

## Requirements

- RSAT Active Directory module installed  
- TechToolbox module loaded  
- Sufficient AD permissions to reset passwords  
- Windows required for clipboard support  

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **Identity** | `string` | AD user identity (sAMAccountName, UPN, or DN). **Required.** |
| **ChangePasswordAtLogon** | `bool` | Whether to force password change at next logon. Default: `true`. |
| **Unlock** | `switch` | Unlocks the account after resetting the password. |
| **Style** | `string` (`Random`, `Readable`, `Passphrase`) | Password generation style. Default: `Readable`. |
| **OutFile** | `string` | Writes the plaintext password to a file. |
| **Clipboard** | `switch` | Copies the password to clipboard (Windows only). |
| **Credential** | `PSCredential` | Reserved for module interface consistency. Not used internally. |

---

## How It Works

### 1. **Password Generation**
Uses TechToolbox’s private generator:

- Length from config:  
  `settings.resetPassword.initialPasswordLength`
- Separator from config:  
  `settings.passwords.default.separator`
- Style from parameter (`Readable` by default)

### 2. **AD Resolution**
Resolves the user with:

```powershell
Get-ADUser -Identity <Identity> -Properties LockedOut, PasswordExpired
```

### 3. **Password Reset**
Performs:

```powershell
Set-ADAccountPassword -Reset
```

### 4. **Optional Actions**
- Unlock account  
- Set ChangePasswordAtLogon  
- Write password to file  
- Copy to clipboard  
- Display password in console (if enabled in config)

### 5. **Output Object**
Returns a structured object:

```text
SamAccountName
DistinguishedName
ChangePasswordAtLogon
Unlocked
PasswordDelivery
Timestamp
```

---

## Examples

### **Reset password, force change at logon, copy to clipboard**
```powershell
Reset-ADPassword -Identity jdoe -ChangePasswordAtLogon -Clipboard
```

### **Reset password, unlock account, write password to file**
```powershell
Reset-ADPassword -Identity jdoe -Unlock -OutFile C:\temp\jdoe_password.txt
```

### **Reset password without forcing change at logon**
```powershell
Reset-ADPassword -Identity jdoe -ChangePasswordAtLogon:$false
```

---

## Notes & Behavior

- Clipboard support requires **Windows**.
- Passwords written to files are **plaintext** — handle with care.
- If the AD module is missing, the function throws a clear error.
- Logging is handled entirely through `Write-Log`.
- The function respects TechToolbox configuration for:
  - Password length  
  - Whether to show passwords in console  
  - Default password style  
