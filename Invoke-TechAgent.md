# Invoke-TechAgent

Sends a prompt to the TechToolbox local agent for automated task execution.

---

## Overview

`Invoke-TechAgent` is a helper function that facilitates interaction with the TechToolbox.Agent C# runtime. It allows users to send natural-language instructions (prompts) to an AI agent, which then performs actions on behalf of the user using available tools and scripts.

> **Note:** The function requires a valid TechToolbox configuration with AI settings defined. It initializes the runtime environment before sending the request.

---

## Parameters

| Parameter | Type | Description |
|----------|------|-------------|
| **Prompt** | `string` | **[Mandatory]** The natural-language instruction for the agent. This is the primary input defining what task to perform. |
| **PromptFile** | `string` | Optional path to a text file containing the prompt. If omitted and `-Prompt` is empty, Invoke-TechAgent attempts to load a default prompt file. |
| **Model** | `string` | Optional Ollama model name (e.g., `llama3`, `mistral`, `qwen2.5-coder`). Specifies which AI model to use for processing the prompt. |
| **MaxIterations** | `int` | Maximum number of tool/reasoning iterations before the agent concludes. Valid range: 1-500. Defaults to `15`. |
| **Quiet** | `switch` | Legacy compatibility switch. Agent traces are now suppressed by default, but this parameter remains for backward compatibility. |
| **ConfirmDestructive** | `switch` | Explicitly authorizes destructive operations for this run. Use with caution. |
| **SignedFilePolicy** | `string` | Policy to use when overwriting an existing Authenticode-signed PowerShell file. Valid values: `'ignore'` (blocks overwrite) or `'strip'` (allows overwrite while removing signature block text). |
| **AutoRetryOnRecursion** | `switch` | Enables a single automatic retry when the C# agent hits an iteration limit. |
| **DisableAutoRetryOnRecursion** | `switch` | Disables recursion-limit auto-retry for this invocation, overriding any global settings. |

---

## OpenAI Implementation

### Supported Providers

Invoke-TechAgent supports multiple LLM providers:
- **ollama** - Local model execution via Ollama runtime
- **openai** - OpenAI cloud API (GPT models)
- **openai-compatible** - Compatible endpoints (e.g., local LLM servers)
- **azure-openai** - Azure OpenAI Service deployments

### API Key Configuration

For cloud providers (OpenAI, OpenAI-Compatible, Azure OpenAI), an API key is required. The key can be configured in several ways:

1. **Encrypted Storage (Recommended)**
   Use `Set-TechAgentApiKey` to securely store the API key using DPAPI encryption:
   ```powershell
   Set-TechAgentApiKey -Provider openai
   ```
   This prompts interactively for the key and stores it encrypted in `config.secrets.json`.

2. **Environment Variable**
   Set the environment variable (default: `TT_AGENT_LLM_API_KEY`) to bypass encrypted storage:
   ```powershell
   $env:TT_AGENT_LLM_API_KEY = "your-api-key"
   Invoke-TechAgent -Prompt "Your instruction" -Provider openai
   ```

3. **Explicit Parameter**
   Pass the API key directly (not recommended for production):
   ```powershell
   $secureKey = Read-Host "Enter API key" -AsSecureString
   Invoke-TechAgent -Prompt "Your instruction" -Provider openai -ApiKey $secureKey
   ```

### Provider Validation

Before using cloud providers, validate the configuration with `Test-TechAgentProvider`:

```powershell
# Validate OpenAI configuration only (no network call)
Test-TechAgentProvider -Provider openai -Model gpt-4o-mini -NoNetwork

# Validate with live API test
Test-TechAgentProvider -Provider openai -Model gpt-4o-mini

# Validate Azure OpenAI deployment
Test-TechAgentProvider -Provider azure-openai -Endpoint "https://your-resource.openai.azure.com" -Deployment "gpt-4o-mini"
```

### Provider-Specific Settings

**OpenAI:**
- Default endpoint: `https://api.openai.com/v1/chat/completions`
- Requires `-Model` parameter (e.g., `gpt-4o`, `gpt-4o-mini`, `gpt-3.5-turbo`)

**OpenAI-Compatible:**
- Requires `-Endpoint` and `-Model` parameters
- Example: Local LLM server or compatible API

**Azure OpenAI:**
- Requires `-Endpoint`, `-Deployment`, and optionally `-ApiVersion`
- Default API version: `2024-10-21`
- Example:
  ```powershell
  Invoke-TechAgent -Prompt "Your instruction" -Provider azure-openai -Endpoint "https://your-resource.openai.azure.com" -Deployment "gpt-4o-mini"
  ```

### Runtime Setup

For Ollama-based providers, ensure the runtime is installed and configured:

```powershell
# Validate Ollama installation
Install-TechAgentRuntime

# Pull a specific model
Install-TechAgentRuntime -PullModel -Model "qwen3.6:35b"
```

---

## Command-Line Usage

### Basic Invocation

Invoke-TechAgent is called from PowerShell as a standard cmdlet:

```powershell
Invoke-TechAgent -Prompt "<your instruction>"
```

### Required Configuration

Before using Invoke-TechAgent, ensure:
- TechToolbox is installed and configured
- AI settings are defined in the TechToolbox configuration (`settings.agent.provider`)
- For cloud providers: API key is configured via `Set-TechAgentApiKey` or environment variable
- For Ollama: Model is available locally (use `Install-TechAgentRuntime -PullModel` to download)

---

## How It Works

### **Agent Execution**
The function calls the TechToolbox.Agent C# runtime and prints the agent's response.
1. Initializes the TechToolbox runtime environment (`Initialize-TechToolboxRuntime`).
2. Loads the prompt from `-Prompt` or `-PromptFile`.
3. Resolves the LLM provider (Ollama, OpenAI, Azure OpenAI, etc.) and API key.
4. Sends the request to the specified AI model (or default if none provided).
5. The agent reasons through the task, potentially invoking other tools or scripts.
6. Returns the final output or error message from the agent.

### **Recursion Handling**
The agent may recursively call itself for complex tasks. The `-AutoRetryOnRecursion` parameter allows a single automatic retry if an iteration limit is hit, helping to complete multi-step processes that exceed default limits.

---

## Examples

### **Basic Prompt Execution**
Send a simple instruction to the agent using the default model.
```powershell
Invoke-TechAgent -Prompt "List all running services on this machine"
```

### **Using a Specific Model**
Specify an Ollama model for processing.
```powershell
Invoke-TechAgent -Prompt "Analyze the disk usage" -Model "qwen2.5-coder"
```

### **Loading Prompt from File**
Use a text file containing the prompt instructions.
```powershell
Invoke-TechAgent -PromptFile "C:\prompts\cleanup.txt"
```

### **Allowing Destructive Actions**
Explicitly authorize destructive operations for this run.
```powershell
Invoke-TechAgent -Prompt "Delete all files in C:\Temp" -ConfirmDestructive
```

### **Setting Maximum Iterations**
Control how many tool calls the agent can make before concluding.
```powershell
Invoke-TechAgent -Prompt "Research and summarize this topic" -MaxIterations 50
```

### **Handling Signed Files**
Specify policy for overwriting signed PowerShell files.
```powershell
Invoke-TechAgent -Prompt "Update script at C:\Scripts\test.ps1" -SignedFilePolicy "strip"
```

### **Using OpenAI Provider**
Invoke the agent with OpenAI cloud API.
```powershell
# First, configure the API key (one-time setup)
Set-TechAgentApiKey -Provider openai

# Then use it in your command
Invoke-TechAgent -Prompt "Summarize this article" -Provider openai -Model gpt-4o-mini
```

### **Using Azure OpenAI**
Invoke the agent with Azure OpenAI deployment.
```powershell
Invoke-TechAgent -Prompt "Analyze system logs" -Provider azure-openai -Endpoint "https://your-resource.openai.azure.com" -Deployment "gpt-4o-mini"
```

### **Validating Provider Configuration**
Test your provider setup before running complex tasks.
```powershell
# Check configuration without network call
Test-TechAgentProvider -NoNetwork

# Test with live API call
Test-TechAgentProvider -Provider openai -Model gpt-4o-mini
```

---

## Notes

- Requires **TechToolbox configuration** to be present with valid AI settings.
- The `-Model` parameter is optional; if omitted, the default model from configuration is used.
- Large prompts may exceed context limits depending on the configured model. Consider splitting large tasks into smaller prompts.
- Use `-ConfirmDestructive` only when you explicitly intend to allow actions that modify or delete data.
- Agent traces are suppressed by default; use `-Quiet` only for backward compatibility with older scripts.
- The `-SignedFilePolicy` parameter is useful when the agent needs to overwrite existing signed PowerShell scripts.
- For complex multi-step tasks, consider using `-AutoRetryOnRecursion` to allow automatic retry on iteration limits.
- For cloud providers (OpenAI, Azure OpenAI), ensure API key is configured via `Set-TechAgentApiKey` or environment variable before use.
- Use `Test-TechAgentProvider` to validate provider configuration and connectivity before running tasks.
- For Ollama-based providers, use `Install-TechAgentRuntime` to validate installation and pull models.

---

## Related Commands

- **Set-TechAgentApiKey** - Securely configure API keys for cloud providers
- **Test-TechAgentProvider** - Validate provider configuration and connectivity
- **Install-TechAgentRuntime** - Setup Ollama runtime and pull models
- **Get-TechToolboxConfig** - View current TechToolbox configuration