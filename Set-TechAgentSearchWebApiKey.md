# Set-TechAgentSearchWebApiKey

## Synopsis

Securely sets, rotates, or clears the TechAgent SEARCH-WEB API key.

## Description

The `Set-TechAgentSearchWebApiKey` cmdlet manages the encrypted SEARCH-WEB API key stored in `config.secrets.json` under `settings.agent.searchWebApiKeyEncrypted`. The key is encrypted using Windows Data Protection API (DPAPI) and is never written to disk in plain text.

This command supports three primary operations:
- **Set**: Store a new or rotated SEARCH-WEB API key.
- **Clear**: Remove the stored DPAPI-encrypted SEARCH-WEB API key from secrets.
- **PassThru**: Return an object summarizing the operation result.

## Syntax

```
Set-TechAgentSearchWebApiKey [[-ApiKey] <SecureString>] [-PassThru] [-WhatIf] [-Confirm] [<CommonParameters>]

Set-TechAgentSearchWebApiKey -Clear [-PassThru] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Parameters

### -ApiKey

The SecureString value of the SEARCH-WEB API key to store.

- **Type**: `SecureString`
- **Required**: No
- **Default value**: None
- **Accept pipeline input**: No
- **Accept wildcard characters**: No

If omitted in Set mode and the session is interactive, you will be prompted with `Read-Host -AsSecureString` to enter the key.

### -Clear

Removes the stored DPAPI-encrypted SEARCH-WEB API key from secrets.

- **Type**: `SwitchParameter`
- **Required**: Yes (when using Clear parameter set)
- **Default value**: False
- **Accept pipeline input**: No
- **Accept wildcard characters**: No

### -PassThru

Returns a custom object summarizing the operation result.

- **Type**: `SwitchParameter`
- **Required**: No
- **Default value**: False
- **Accept pipeline input**: No
- **Accept wildcard characters**: No

When specified, returns an object with the following properties:
- `Success` (Boolean): Indicates whether the operation succeeded.
- `Action` (String): The action performed ('Set' or 'Clear').
- `SecretsPath` (String): Path to the secrets file.
- `KeyStored` (Boolean): Indicates whether a key is now stored.
- `Detail` (String): Human-readable description of the result.

## Examples

### Example 1: Set API key interactively

```
Set-TechAgentSearchWebApiKey
```

Prompts for the SEARCH-WEB API key and stores it encrypted in secrets.

### Example 2: Set API key from SecureString

```
$secureKey = Read-Host "Enter SEARCH-WEB API key" -AsSecureString
Set-TechAgentSearchWebApiKey -ApiKey $secureKey
```

Stores the provided SecureString as the encrypted SEARCH-WEB API key.

### Example 3: Clear stored API key

```
Set-TechAgentSearchWebApiKey -Clear
```

Removes the stored DPAPI-encrypted SEARCH-WEB API key from secrets.

### Example 4: Return operation result

```
$result = Set-TechAgentSearchWebApiKey -PassThru
$result | Format-List
```

Returns and displays a summary object of the operation.

## Notes

- This command uses DPAPI encryption, which ties the stored key to the current user and machine.
- The key is never written to disk in plain text.
- Supports `-WhatIf` and `-Confirm` for safe execution in non-interactive environments.
- Requires an interactive session if `-ApiKey` is not provided.

## Related Links

- [Online documentation](https://dan-damit.github.io/TechToolbox-Docs/Set-TechAgentSearchWebApiKey)
- `Read-Secrets`
- `Write-Secrets`
- `ConvertFrom-SecureString`
- `Test-TTInteractive`

## Input Type

None. This command does not accept pipeline input.

## Output Type

`System.Management.Automation.PSCustomObject` (when `-PassThru` is specified).

## See Also

- `about_TechToolbox_AI`
- `about_TechToolbox_Secrets`
- `Get-TechAgentSearchWebApiKey`
