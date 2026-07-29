# Test-TechAgentProvider

## Synopsis
Validates TechAgent LLM provider configuration and connectivity.

## Description
Test-TechAgentProvider performs a self-test for the LLM provider used by Invoke-TechAgent. It validates required configuration values and can run a minimal live API request to confirm authentication and response parsing.

Supported providers:
- ollama
- openai
- openai-compatible
- azure-openai

The command resolves settings from explicit parameters first, then from configuration, then from built-in defaults where applicable.

For cloud providers, API key resolution supports:
- Encrypted key from config.secrets.json (DPAPI-protected)
- Environment variable lookup (default: TT_AGENT_LLM_API_KEY)
- Optional interactive prompt to enter and persist a key when missing

## Syntax
Test-TechAgentProvider [-Provider <string>] [-Model <string>] [-Endpoint <string>] [-Deployment <string>] [-ApiVersion <string>] [-ApiKeyEnvVar <string>] [-ApiKeyEncrypted] [-ApiKeyEncryptedBlob <string>] [-NoNetwork] [-TimeoutSeconds <int>]

## Parameters
### -Provider
LLM provider to validate.

Allowed values:
- ollama
- openai
- openai-compatible
- azure-openai

Default behavior:
- Uses settings.agent.provider when available
- Falls back to ollama when not set

### -Model
Model identifier to validate.

Behavior by provider:
- Required for openai
- Required for openai-compatible
- Optional for ollama (if provided, must exist locally)
- Not required for azure-openai (deployment is used)

### -Endpoint
Optional endpoint URL.

Behavior by provider:
- Required for azure-openai
- Required for openai-compatible
- Optional for openai (defaults to https://api.openai.com/v1/chat/completions)
- Not used for ollama validation

### -Deployment
Azure OpenAI deployment name.

Behavior:
- Required when Provider is azure-openai

### -ApiVersion
API version for Azure OpenAI requests.

Default:
- 2024-10-21

### -ApiKeyEnvVar
Name of the environment variable containing the cloud API key.

Default behavior:
- Uses settings.agent.apiKeyEnvVar when configured
- Falls back to TT_AGENT_LLM_API_KEY

### -ApiKeyEncrypted
Switch that prefers encrypted config-based API key resolution and skips environment variable lookup.

Use this when you want to force stored secret usage.

### -ApiKeyEncryptedBlob
Explicit encrypted API key blob to use for this run.

This value is expected to be a DPAPI-encrypted string compatible with ConvertTo-SecureString.

### -NoNetwork
Validates configuration only and skips live provider network calls.

Useful for offline checks and CI validation.

### -TimeoutSeconds
Timeout for live network request operations.

## Behavior details
### Provider-specific validation
- ollama
  - Verifies ollama executable is available in PATH
  - Runs ollama list to enumerate local models
  - If Model is provided, confirms it exists locally
- openai
  - Requires Model
  - Uses OpenAI endpoint unless Endpoint is explicitly provided
- openai-compatible
  - Requires Endpoint and Model
- azure-openai
  - Requires Endpoint and Deployment
  - Uses ApiVersion in request URI

### Live test request
When -NoNetwork is not used, the command sends a minimal chat completion request with deterministic settings:
- temperature = 0
- top_p = 1
- max_tokens = 64
- stream = false
- response_format = json_object

Success requires non-empty response content in the first choice message.

## Output
Returns a PSCustomObject with these fields:
- Success
- Provider
- Model
- Endpoint
- Deployment
- ApiVersion
- ApiKeyEnvVar
- ApiKeySource
- ApiKeyPresent
- PerformedLiveNetworkTest
- Status
- Detail
- HttpStatusCode
- ResponsePreview

Common Status values:
- NotStarted
- ConfigurationValidated
- ConfigurationError
- Connected
- ProviderError

## Examples
### 1) Validate current/default provider configuration only
Test-TechAgentProvider -NoNetwork

### 2) Validate OpenAI with explicit model and live call
Test-TechAgentProvider -Provider openai -Model gpt-4o-mini

### 3) Validate OpenAI-compatible endpoint
Test-TechAgentProvider -Provider openai-compatible -Endpoint "https://example-llm.local/v1/chat/completions" -Model my-model

### 4) Validate Azure OpenAI deployment
Test-TechAgentProvider -Provider azure-openai -Endpoint "https://myresource.openai.azure.com" -Deployment "gpt-4o-mini" -ApiVersion "2024-10-21"

### 5) Force encrypted key usage only
Test-TechAgentProvider -Provider openai -Model gpt-4o-mini -ApiKeyEncrypted

## API key storage notes
For cloud providers, if no key is resolved, the command can prompt to store an encrypted key in config.secrets.json. The stored value is DPAPI-protected and tied to the local security context.

If an environment variable name is configured, the entered key may also be set for the current process session.

## Related
- Invoke-TechAgent
- Set-TechAgentApiKey
- Use-TechAgentTaskTemplate
