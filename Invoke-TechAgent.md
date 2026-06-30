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

## How It Works

### **Agent Execution**
The function calls the TechToolbox.Agent C# runtime and prints the agent's response.
1. Initializes the TechToolbox runtime environment (`Initialize-TechToolboxRuntime`).
2. Loads the prompt from `-Prompt` or `-PromptFile`.
3. Sends the request to the specified AI model (or default if none provided).
4. The agent reasons through the task, potentially invoking other tools or scripts.
5. Returns the final output or error message from the agent.

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

---

## Notes

- Requires **TechToolbox configuration** to be present with valid AI settings.
- The `-Model` parameter is optional; if omitted, the default model from configuration is used.
- Large prompts may exceed context limits depending on the configured model. Consider splitting large tasks into smaller prompts.
- Use `-ConfirmDestructive` only when you explicitly intend to allow actions that modify or delete data.
- Agent traces are suppressed by default; use `-Quiet` only for backward compatibility with older scripts.