## Invoke-TechAgent

Sends a prompt to the TechToolbox local agent runtime and returns the agent response.

## What this command does

- Calls the TechToolbox.Agent C# runtime from PowerShell.
- Resolves prompt input from one source only:
  - `-Prompt`
  - `-PromptFile`
  - default prompt file from config (`settings.agent.defaultPromptFile`) or fallback `AI\prompt.txt`
- Applies model/provider settings from parameters first, then config defaults.
- Supports execution control modes (`execute`, `plan`, `analyze`).
- Supports output format contracts (`markdown`, `plain-text`, `json`).

## Syntax

`Invoke-TechAgent [-Prompt <string>] [-PromptFile <string>] [-Model <string>] [-Provider <string>] [-Endpoint <string>] [-Deployment <string>] [-ApiVersion <string>] [-ApiKeyEnvVar <string>] [-ApiKeyEncrypted] [-ApiKeyEncryptedBlob <string>] [-DisableApiKeyPrompt] [-MaxIterations <int>] [-PromptHistoryItems <int>] [-ExecutionMode <string>] [-StrictPromptPreflight] [-OutputContract <string>] [-QualityProfile <string>] [-Quiet] [-ConfirmDestructive] [-SignedFilePolicy <string>] [-AutoRetryOnRecursion] [-DisableAutoRetryOnRecursion] [-NoTranscript <bool>] [-AllowMetaTools]`

## Parameters

| Name | Type | Allowed values / range | Default | Description |
| - | - | - | - | - |
| `Prompt` | `string` | non-empty when provided | none | Natural-language instruction for the agent. |
| `PromptFile` | `string` | valid file path | none | Path to a prompt text file. Cannot be used with `-Prompt`. |
| `Model` | `string` | any non-empty model name | from config if set | Model name (for example `llama3`, `mistral`, `qwen2.5-coder`). |
| `Provider` | `string` | `ollama`, `openai`, `openai-compatible`, `azure-openai` | `ollama` (if not set in config/param) | LLM provider selection. |
| `Endpoint` | `string` | URL/string | none | Provider endpoint. For Azure OpenAI, use resource endpoint (for example `https://name.openai.azure.com`). |
| `Deployment` | `string` | string | none | Azure OpenAI deployment name. |
| `ApiVersion` | `string` | string | none | API version for providers that require it. |
| `ApiKeyEnvVar` | `string` | non-empty env var name | `TT_AGENT_LLM_API_KEY` | Environment variable name used for cloud API key lookup. |
| `ApiKeyEncrypted` | `switch` | n/a | off | Prefer encrypted config-based API key resolution and skip env var lookup. |
| `ApiKeyEncryptedBlob` | `string` | DPAPI blob text | none | Optional encrypted API key blob (from `ConvertFrom-SecureString`), overrides `settings.agent.apiKeyEncrypted`. |
| `DisableApiKeyPrompt` | `switch` | n/a | off | Disable interactive API key capture/storage prompts. |
| `MaxIterations` | `int` | `1..500` | `15` | Maximum tool/reasoning iterations before conclusion. |
| `PromptHistoryItems` | `int` | `0..20` | runtime/config dependent | Number of recent memory history entries injected into prompt context. `0` disables history injection. |
| `ExecutionMode` | `string` | `execute`, `plan`, `analyze` | `execute` | Controls whether tools run or the agent returns no-tool planning/analysis. |
| `StrictPromptPreflight` | `switch` | n/a | off | Treat prompt preflight warnings as blocking validation failures. |
| `OutputContract` | `string` | `markdown`, `plain-text`, `json` | runtime/config dependent | Required final response format. |
| `QualityProfile` | `string` | `precise`, `balanced`, `creative` | runtime/config dependent | Sampling/quality profile for response behavior. |
| `Quiet` | `switch` | n/a | off | Legacy compatibility switch; traces are suppressed by default. |
| `ConfirmDestructive` | `switch` | n/a | off | Explicitly authorizes destructive operations for this run. |
| `SignedFilePolicy` | `string` | `ignore`, `strip` | runtime/config dependent | Behavior when overwriting Authenticode-signed PowerShell files. |
| `AutoRetryOnRecursion` | `switch` | n/a | off | Enable one automatic retry when iteration/recursion limit is hit. |
| `DisableAutoRetryOnRecursion` | `switch` | n/a | off | Disable recursion-limit auto-retry for this invocation. |
| `NoTranscript` | `bool` | `true`/`false` | `true` | Disable per-run console transcript logging when `true`. |
| `AllowMetaTools` | `switch` | n/a | off | Allow higher-order meta tools (for example `Invoke-TechAgent`) for this run. |

## Prompt source behavior

- Exactly one explicit prompt source is allowed:
  - `-Prompt`
  - `-PromptFile`
- If neither is provided, the command attempts:
  - `settings.agent.defaultPromptFile` from config
  - fallback `AI\prompt.txt` under module root
- If the resolved prompt file is missing or empty, the command throws.

## Practical examples

### Basic run

`Invoke-TechAgent "Run system diagnostics and summarize findings."`

### Use a prompt file

`Invoke-TechAgent -PromptFile "AI\prompt.txt"`

### Cloud provider with explicit endpoint/deployment

`Invoke-TechAgent -Prompt "Summarize mailbox audit anomalies." -Provider azure-openai -Endpoint "https://name.openai.azure.com" -Deployment "gpt-4o" -ApiVersion "2024-02-15-preview"`

### Plan-only mode with strict output contract

`Invoke-TechAgent -Prompt "Propose a safe remediation sequence." -ExecutionMode plan -OutputContract markdown -QualityProfile precise`

### Non-interactive automation

`Invoke-TechAgent -PromptFile "AI\job-prompt.txt" -DisableApiKeyPrompt -NoTranscript $true`

## Notes for this repository

- This command is documented from `C:\repos\TechToolbox\Public\AI\Invoke-TechAgent.ps1`.
- Provider/model defaults are resolved from `settings.agent` when not explicitly passed.
- Use `-ConfirmDestructive` only when the task explicitly requires destructive actions.

## Reference

- Script help link: `https://dan-damit.github.io/TechToolbox-Docs/Invoke-TechAgent`
