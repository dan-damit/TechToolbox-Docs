# Set-TechAgentApiKey

## SYNOPSIS
Securely sets, rotates, or clears the TechAgent cloud API key.

## SYNTAX

```powershell
Set-TechAgentApiKey [[-ApiKey] <SecureString>] [[-Provider] <String>] [-PassThru] [-WhatIf] [-Confirm] [<CommonParameters>]

Set-TechAgentApiKey -Clear [-Provider] <String> [-PassThru] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## DESCRIPTION
The `Set-TechAgentApiKey` function manages `settings.agent.apiKeyEncrypted` in `config.secrets.json` using DPAPI encryption.

When setting a key, the command stores only the DPAPI-encrypted value produced from a `SecureString`. Plain-text API keys are never written to disk by this function.

If `-ApiKey` is omitted in Set mode and the session is interactive, the function prompts for the key using `Read-Host -AsSecureString`.

If `-Clear` is used, the function removes `settings.agent.apiKeyEncrypted` from secrets.

This command supports `ShouldProcess`, so `-WhatIf` and `-Confirm` are available.

## EXAMPLES

### Example 1: Prompt interactively and store key
```powershell
Set-TechAgentApiKey
```

**Description:**
Prompts for the cloud API key as a secure string in an interactive session, encrypts it with DPAPI, and stores it in `settings.agent.apiKeyEncrypted`.

### Example 2: Store a provided secure key with provider context
```powershell
$secureKey = Read-Host "Enter cloud API key" -AsSecureString
Set-TechAgentApiKey -ApiKey $secureKey -Provider openai
```

**Description:**
Stores the provided secure key as a DPAPI-encrypted secret and records provider context in the operation detail message.

### Example 3: Clear stored key
```powershell
Set-TechAgentApiKey -Clear
```

**Description:**
Removes the stored encrypted key from `settings.agent.apiKeyEncrypted`.

### Example 4: Return operation result object
```powershell
Set-TechAgentApiKey -PassThru
```

**Description:**
Performs the set operation (prompting interactively if needed) and returns a summary object with fields such as `Success`, `Action`, `Provider`, `SecretsPath`, `KeyStored`, and `Detail`.

## PARAMETERS

### -ApiKey
Secure API key value to store.

If omitted in Set mode and running interactively, you are prompted with `Read-Host -AsSecureString`.

```yaml
  Required:                    false
  Position:                    1
  Default value:
  Accept pipeline input?       False
  Accept wildcard characters?  false
```

### -Provider
Optional provider context used for validation and messaging.

Allowed values:
- `openai`
- `openai-compatible`
- `azure-openai`

```yaml
  Required:                    false
  Position:                    2
  Default value:
  Accept pipeline input?       False
  Accept wildcard characters?  false
```

### -Clear
Removes the stored DPAPI-encrypted cloud API key from secrets.

```yaml
  Required:                    true (Clear parameter set)
  Position:                    named
  Default value:               false
  Accept pipeline input?       False
  Accept wildcard characters?  false
```

### -PassThru
Returns an object summarizing the operation.

```yaml
  Required:                    false
  Position:                    named
  Default value:               false
  Accept pipeline input?       False
  Accept wildcard characters?  false
```

### -WhatIf
Shows what would happen if the command runs. The command is not executed.

```yaml
  Required:                    false
  Position:                    named
  Default value:               false
  Accept pipeline input?       False
  Accept wildcard characters?  false
```

### -Confirm
Prompts for confirmation before running the command.

```yaml
  Required:                    false
  Position:                    named
  Default value:               false
  Accept pipeline input?       False
  Accept wildcard characters?  false
```

## INPUTS
None. You cannot pipe objects to `Set-TechAgentApiKey`.

## OUTPUTS
By default, none.

When `-PassThru` is specified, returns a `PSCustomObject` with operation details.

## NOTES
- Uses DPAPI via `ConvertFrom-SecureString` to protect the key at rest.
- Writes to secrets through internal `Read-Secrets` and `Write-Secrets` helpers.
- In non-interactive sessions, `-ApiKey` must be provided or the command throws an error.
- Initializes runtime with `Initialize-TechToolboxRuntime`.

## LINKS
- https://dan-damit.github.io/TechToolbox-Docs/Set-TechAgentApiKey
- [TechToolbox](https://github.com/dan-damit/TechToolbox)
