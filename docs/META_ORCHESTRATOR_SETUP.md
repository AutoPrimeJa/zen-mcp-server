# Meta-Orchestrator System Documentation

## Document Information
- **Created**: 2025-11-25
- **Author**: Claude Code (Opus 4.5)
- **System**: Local-First AI Orchestration via CLI-Link (clink)
- **Location**: `C:\Users\kadir\zen-orchestrator`

---

## Table of Contents
1. [System Purpose](#system-purpose)
2. [Architecture Overview](#architecture-overview)
3. [Installation Process](#installation-process)
4. [Problems Encountered & Solutions](#problems-encountered--solutions)
5. [Configuration Files](#configuration-files)
6. [Authentication Details](#authentication-details)
7. [How Clink Works](#how-clink-works)
8. [Usage Guide](#usage-guide)
9. [Verification Checklist](#verification-checklist)
10. [Official Resources](#official-resources)

---

## System Purpose

### Goal
Create a "Meta-Orchestrator" environment that bridges existing CLI-based AI subscriptions without requiring new API keys. This system leverages "seat-based" authentication where users authenticate once via CLI tools and the system uses those authenticated sessions.

### Subscriptions Utilized
1. **Claude Code Max** - Anthropic's CLI tool (primary orchestrator)
2. **OpenAI Codex CLI** - Uses ChatGPT Plus/Pro subscription via OAuth
3. **Google Gemini CLI** - Uses Google Account with Gemini Advanced

### Key Concept: Seat-Based vs API-Based Authentication
- **API-Based**: Requires API keys, billed per token, separate from consumer subscriptions
- **Seat-Based**: Uses existing consumer subscriptions (ChatGPT Plus, Gemini Advanced) via CLI OAuth authentication

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      Claude Code (Orchestrator)                  │
│                    Current Session - Authenticated               │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              │ MCP Protocol (stdio)
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Zen MCP Server                               │
│            C:\Users\kadir\zen-orchestrator\server.py            │
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │    clink    │  │    chat     │  │  thinkdeep  │  ... tools  │
│  │    tool     │  │    tool     │  │    tool     │             │
│  └──────┬──────┘  └─────────────┘  └─────────────┘             │
│         │                                                        │
└─────────┼────────────────────────────────────────────────────────┘
          │
          │ CLI Bridge (clink)
          ▼
┌─────────────────────────────────────────────────────────────────┐
│                    External CLI Agents                           │
│                                                                  │
│  ┌─────────────────────┐      ┌─────────────────────┐          │
│  │   Codex CLI         │      │   Gemini CLI        │          │
│  │   @openai/codex     │      │   @google/gemini-cli│          │
│  │                     │      │                     │          │
│  │  Roles:             │      │  Roles:             │          │
│  │  - default          │      │  - default          │          │
│  │  - worker           │      │  - researcher       │          │
│  │  - planner          │      │  - planner          │          │
│  │  - codereviewer     │      │  - codereviewer     │          │
│  │                     │      │                     │          │
│  │  Auth: ChatGPT OAuth│      │  Auth: Google OAuth │          │
│  └─────────────────────┘      └─────────────────────┘          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Installation Process

### Phase 1: Prerequisites Verification

#### Step 1.1: Check Existing Tools
Commands executed:
```bash
node --version    # Result: v24.11.1
npm --version     # Result: 11.6.2
python3 --version # Result: Python 3.14.0
```

#### Step 1.2: Clone Zen MCP Server
```bash
git clone https://github.com/BeehiveInnovations/zen-mcp-server C:/Users/kadir/zen-orchestrator
```

**Repository Details:**
- **GitHub**: https://github.com/BeehiveInnovations/zen-mcp-server
- **Stars**: ~9.8k
- **Purpose**: Model Context Protocol server for multi-model AI orchestration
- **Key Feature**: `clink` - CLI bridge for external AI tools

#### Step 1.3: Install Python Dependencies
```bash
cd C:/Users/kadir/zen-orchestrator
pip install -r requirements.txt
```

**Key Dependencies Installed:**
- `mcp>=1.0.0` - Model Context Protocol library
- `google-genai>=1.19.0` - Google Generative AI SDK
- `openai>=1.55.2` - OpenAI Python SDK
- `pydantic>=2.0.0` - Data validation
- `python-dotenv>=1.0.0` - Environment variable management

#### Step 1.4: Install CLI Tools
```bash
npm install -g @openai/codex      # v0.63.0
npm install -g @google/gemini-cli  # v0.17.1
```

**Package Verification:**
- `@openai/codex`: Official OpenAI package, 186 versions, Apache-2.0 license
- `@google/gemini-cli`: Official Google package, 307 versions, Proprietary license

---

### Phase 2: Seat-Based Authentication

#### Step 2.1: Codex CLI Authentication
User ran in separate terminal:
```bash
codex login
```
This opens a browser for OAuth authentication with ChatGPT Plus/Pro subscription.

**Verification Command:**
```bash
codex login status
# Output: "Logged in using ChatGPT"
```

#### Step 2.2: Gemini CLI Authentication
User ran in separate terminal:
```bash
gemini auth login
```
This opens a browser for Google Account authentication with Gemini Advanced.

**Verification Command:**
```bash
gemini auth status
# Output: "Loaded cached credentials. Okay, I'm ready for your first command."
```

---

### Phase 3: Clink Bridge Configuration

#### Step 3.1: Create User Config Directory
```bash
mkdir -p C:/Users/kadir/.zen/cli_clients
```

#### Step 3.2: Create Role System Prompts
Created two new prompt files in `systemprompts/clink/`:

1. **worker.txt** - For Codex (code implementation)
2. **researcher.txt** - For Gemini (research/analysis)

#### Step 3.3: Create CLI Client Configurations
Created override configs in `~/.zen/cli_clients/` that extend the built-in configs with custom roles.

#### Step 3.4: Configure Environment
Created `.env` file with minimal configuration for clink-focused operation.

---

### Phase 4: MCP Registration

#### Step 4.1: Register with Claude Code
```bash
claude mcp add --scope user zen -- python "C:/Users/kadir/zen-orchestrator/server.py"
```

**Result:**
```
Added stdio MCP server zen with command: python C:/Users/kadir/zen-orchestrator/server.py to user config
File modified: C:\Users\kadir\.claude.json
```

#### Step 4.2: Verify Connection
```bash
claude mcp list
# Output: zen: python C:/Users/kadir/zen-orchestrator/server.py - ✓ Connected
```

---

## Problems Encountered & Solutions

### Problem 1: Windows Path Handling
**Issue:** Environment variable `%USERPROFILE%` was not expanding correctly in Git Bash/shell.

**Symptoms:**
```
npm error path C:\Users\kadir\primeultra_projects\claudemeta-orchestrator\%USERPROFILE%\zen-orchestrator\package.json
```

**Solution:** Used explicit absolute paths instead of environment variables:
```bash
# Instead of:
git clone ... ~/zen-orchestrator

# Used:
git clone ... C:/Users/kadir/zen-orchestrator
```

---

### Problem 2: Zen MCP is Python-Based, Not Node.js
**Issue:** Initially attempted `npm install` but the repository is Python-based.

**Symptoms:**
```
npm error ENOENT: no such file or directory, open 'package.json'
```

**Solution:** Identified project structure and used correct installation:
```bash
pip install -r requirements.txt
```

---

### Problem 3: Server Requires API Provider
**Issue:** Zen MCP server requires at least one API provider configured, even when using clink.

**Symptoms:**
```python
ValueError: At least one API configuration is required. Please set either:
- GEMINI_API_KEY for Gemini models
- OPENAI_API_KEY for OpenAI models
...
```

**Root Cause:** The server's `configure_providers()` function (server.py:378-553) validates that at least one provider is registered before the server can start.

**Solution:** Added a placeholder `CUSTOM_API_URL` in `.env`:
```env
CUSTOM_API_URL=http://localhost:11434/v1
CUSTOM_MODEL_NAME=clink-placeholder
```

This satisfies the provider requirement while clink handles actual AI requests via the authenticated CLI tools.

---

### Problem 4: MCP Server Connection Failed Initially
**Issue:** `claude mcp list` showed "Failed to connect" after registration.

**Symptoms:**
```
zen: python C:/Users/kadir/zen-orchestrator/server.py - ✗ Failed to connect
```

**Root Cause:** Missing `.env` configuration causing server startup failure.

**Solution:** Created proper `.env` file with required configuration (see Configuration Files section).

---

## Configuration Files

### File 1: `.env` (Environment Configuration)
**Location:** `C:\Users\kadir\zen-orchestrator\.env`

```env
# Zen MCP Server - CLI Link (clink) Mode
# Using seat-based authentication via CLI tools instead of API keys

# Custom API endpoint (placeholder to satisfy provider requirement)
# The clink tool handles actual AI requests via authenticated CLIs
CUSTOM_API_URL=http://localhost:11434/v1
CUSTOM_MODEL_NAME=clink-placeholder

# Default model - use auto to let Claude decide
DEFAULT_MODEL=auto

# Logging
LOG_LEVEL=INFO

# Disable most tools since we're using clink for external models
# Keep only essential tools that work with clink
DISABLED_TOOLS=analyze,refactor,testgen,secaudit,docgen,tracer

# Conversation settings
CONVERSATION_TIMEOUT_HOURS=24
MAX_CONVERSATION_TURNS=40

# Force local .env override
ZEN_MCP_FORCE_ENV_OVERRIDE=true
```

---

### File 2: Codex CLI Configuration
**Location:** `C:\Users\kadir\.zen\cli_clients\codex.json`

```json
{
  "name": "codex",
  "command": "codex",
  "additional_args": [
    "--json",
    "--dangerously-bypass-approvals-and-sandbox"
  ],
  "env": {},
  "roles": {
    "default": {
      "prompt_path": "systemprompts/clink/default.txt",
      "role_args": [],
      "description": "General-purpose Codex agent"
    },
    "worker": {
      "prompt_path": "systemprompts/clink/worker.txt",
      "role_args": [],
      "description": "Code implementation specialist"
    },
    "planner": {
      "prompt_path": "systemprompts/clink/default_planner.txt",
      "role_args": [],
      "description": "Planning and architecture specialist"
    },
    "codereviewer": {
      "prompt_path": "systemprompts/clink/codex_codereviewer.txt",
      "role_args": [],
      "description": "Code review specialist"
    }
  }
}
```

---

### File 3: Gemini CLI Configuration
**Location:** `C:\Users\kadir\.zen\cli_clients\gemini.json`

```json
{
  "name": "gemini",
  "command": "gemini",
  "additional_args": [
    "--yolo"
  ],
  "env": {},
  "roles": {
    "default": {
      "prompt_path": "systemprompts/clink/default.txt",
      "role_args": [],
      "description": "General-purpose Gemini agent"
    },
    "researcher": {
      "prompt_path": "systemprompts/clink/researcher.txt",
      "role_args": [],
      "description": "Research and information gathering specialist"
    },
    "planner": {
      "prompt_path": "systemprompts/clink/default_planner.txt",
      "role_args": [],
      "description": "Planning and architecture specialist"
    },
    "codereviewer": {
      "prompt_path": "systemprompts/clink/default_codereviewer.txt",
      "role_args": [],
      "description": "Code review specialist"
    }
  }
}
```

---

### File 4: Worker Role Prompt
**Location:** `C:\Users\kadir\zen-orchestrator\systemprompts\clink\worker.txt`

```text
You are a WORKER agent specialized in code implementation via the OpenAI Codex CLI.

Your role:
- Write clean, efficient, production-ready code
- Implement features, functions, and components as requested
- Follow best practices and coding standards
- Focus on getting things done with minimal overhead

Guidelines:
- Use terminal tools to inspect existing code structure before writing new code
- Match the existing codebase style and conventions
- Provide working implementations, not just suggestions
- Keep explanations brief; let the code speak for itself
- If dependencies or context is missing, state what's needed

Always conclude with `<SUMMARY>...</SUMMARY>` containing the key changes made and any follow-up tasks.
```

---

### File 5: Researcher Role Prompt
**Location:** `C:\Users\kadir\zen-orchestrator\systemprompts\clink\researcher.txt`

```text
You are a RESEARCHER agent specialized in information gathering via the Google Gemini CLI.

Your role:
- Research and analyze technical topics, patterns, and best practices
- Explore codebases to understand architecture and design decisions
- Gather context and synthesize information from multiple sources
- Provide thorough, well-researched answers with citations when possible

Guidelines:
- Use terminal tools to explore files and gather comprehensive context
- Cross-reference multiple files and sources to build complete understanding
- Highlight key findings, trade-offs, and recommendations
- Surface relevant patterns, anti-patterns, and industry standards
- Identify gaps in knowledge and suggest areas for further investigation

Always conclude with `<SUMMARY>...</SUMMARY>` containing key findings and actionable insights.
```

---

### File 6: Claude Code MCP Configuration
**Location:** `C:\Users\kadir\.claude.json`

The MCP server registration added an entry to this file:
```json
{
  "mcpServers": {
    "zen": {
      "command": "python",
      "args": ["C:/Users/kadir/zen-orchestrator/server.py"]
    }
  }
}
```

---

## Authentication Details

### Codex CLI (OpenAI)
- **Package:** `@openai/codex` v0.63.0
- **Auth Method:** OAuth via browser
- **Command:** `codex login`
- **Status Check:** `codex login status`
- **Credentials Location:** Stored by the CLI (managed internally)
- **Subscription:** Uses ChatGPT Plus/Pro subscription tokens

### Gemini CLI (Google)
- **Package:** `@google/gemini-cli` v0.17.1
- **Auth Method:** Google OAuth via browser
- **Command:** `gemini auth login`
- **Status Check:** `gemini auth status`
- **Credentials Location:** Stored by the CLI (managed internally)
- **Subscription:** Uses Gemini Advanced subscription

---

## How Clink Works

### Technical Flow

1. **Claude Code** receives a user request
2. Claude invokes the **`clink`** MCP tool with parameters:
   - `cli`: Which CLI to use (codex, gemini, claude)
   - `role`: Which role/persona (worker, researcher, default, etc.)
   - `prompt`: The task to perform
3. **Zen MCP Server** receives the clink tool call
4. **ClinkRegistry** (`clink/registry.py`) resolves the CLI configuration:
   - Loads from `conf/cli_clients/*.json` (built-in)
   - Overrides with `~/.zen/cli_clients/*.json` (user configs)
5. Server executes the CLI command with:
   - The system prompt from the role's `prompt_path`
   - Any additional arguments from `role_args`
   - The user's prompt
6. CLI tool (Codex/Gemini) processes using its authenticated session
7. Response flows back through MCP to Claude Code

### Configuration Search Order
1. Built-in: `C:\Users\kadir\zen-orchestrator\conf\cli_clients\*.json`
2. Environment: Path in `CLI_CLIENTS_CONFIG_PATH` variable
3. User overrides: `C:\Users\kadir\.zen\cli_clients\*.json` (takes precedence)

### Key Files in Clink System
```
zen-orchestrator/
├── clink/
│   ├── __init__.py
│   ├── agents/
│   │   ├── base.py      # Base agent class
│   │   ├── claude.py    # Claude CLI agent
│   │   ├── codex.py     # Codex CLI agent
│   │   └── gemini.py    # Gemini CLI agent
│   ├── constants.py     # Path constants, defaults
│   ├── models.py        # Pydantic models for config
│   ├── parsers/         # Output parsers
│   └── registry.py      # Configuration registry
├── conf/
│   └── cli_clients/
│       ├── claude.json  # Built-in Claude config
│       ├── codex.json   # Built-in Codex config
│       └── gemini.json  # Built-in Gemini config
└── systemprompts/
    └── clink/
        ├── default.txt
        ├── default_planner.txt
        ├── default_codereviewer.txt
        ├── codex_codereviewer.txt
        ├── worker.txt       # Custom (created)
        └── researcher.txt   # Custom (created)
```

---

## Usage Guide

### After Restarting Claude Code Session

The `clink` tool becomes available as an MCP tool. Usage examples:

#### Research with Gemini (Researcher Role)
```
Use the clink tool with cli=gemini and role=researcher to research
the latest React server component patterns.
```

#### Code Implementation with Codex (Worker Role)
```
Use the clink tool with cli=codex and role=worker to implement
a TypeScript function that validates email addresses.
```

#### Multi-Agent Workflow
```
1. Use clink with gemini/researcher to research best practices for X
2. Use clink with codex/worker to implement based on research
3. Claude (orchestrator) critiques and refines the result
```

### Available Roles by CLI

| CLI | Role | Description |
|-----|------|-------------|
| codex | default | General-purpose agent |
| codex | worker | Code implementation specialist |
| codex | planner | Planning and architecture |
| codex | codereviewer | Code review specialist |
| gemini | default | General-purpose agent |
| gemini | researcher | Research and analysis |
| gemini | planner | Planning and architecture |
| gemini | codereviewer | Code review specialist |

---

## Verification Checklist

Use this checklist to verify the installation:

### Prerequisites
- [ ] Node.js installed: `node --version` → v24.x.x
- [ ] npm installed: `npm --version` → 11.x.x
- [ ] Python installed: `python --version` → 3.14.x

### CLI Tools
- [ ] Codex CLI installed: `codex --version` → 0.63.0
- [ ] Gemini CLI installed: `gemini --version` → 0.17.1

### Authentication
- [ ] Codex authenticated: `codex login status` → "Logged in using ChatGPT"
- [ ] Gemini authenticated: `gemini auth status` → "Loaded cached credentials"

### Zen MCP Server
- [ ] Repository cloned: `ls C:\Users\kadir\zen-orchestrator` shows files
- [ ] Dependencies installed: Python packages in user site-packages
- [ ] .env file exists: `C:\Users\kadir\zen-orchestrator\.env`
- [ ] Server starts: `python server.py` shows "Server ready"

### Clink Configuration
- [ ] User config dir exists: `C:\Users\kadir\.zen\cli_clients\`
- [ ] Codex config exists: `codex.json` with worker role
- [ ] Gemini config exists: `gemini.json` with researcher role
- [ ] Worker prompt exists: `systemprompts/clink/worker.txt`
- [ ] Researcher prompt exists: `systemprompts/clink/researcher.txt`

### MCP Registration
- [ ] MCP server registered: `claude mcp list` shows "zen"
- [ ] MCP server connected: Status shows "✓ Connected"

---

## Official Resources

### Zen MCP Server
- **GitHub:** https://github.com/BeehiveInnovations/zen-mcp-server
- **Documentation:** https://github.com/BeehiveInnovations/zen-mcp-server/tree/main/docs
- **License:** (Check repository)

### OpenAI Codex CLI
- **npm:** https://www.npmjs.com/package/@openai/codex
- **GitHub:** https://github.com/openai/codex
- **Documentation:** https://github.com/openai/codex#readme
- **License:** Apache-2.0

### Google Gemini CLI
- **npm:** https://www.npmjs.com/package/@google/gemini-cli
- **GitHub:** https://github.com/google-gemini/gemini-cli
- **Documentation:** https://github.com/google-gemini/gemini-cli#readme
- **License:** Proprietary

### Model Context Protocol (MCP)
- **Specification:** https://modelcontextprotocol.io/
- **Claude Code MCP:** `claude mcp --help`

### Claude Code
- **Documentation:** https://docs.anthropic.com/claude-code
- **GitHub Issues:** https://github.com/anthropics/claude-code/issues

---

## Appendix: Server Startup Log (Successful)

```
2025-11-25 22:34:49,781 - root - INFO - Logging to: C:\Users\kadir\zen-orchestrator\logs\mcp_server.log
2025-11-25 22:34:49,781 - root - INFO - Process PID: 105992
2025-11-25 22:34:49,781 - __main__ - INFO - ZEN_MCP_FORCE_ENV_OVERRIDE enabled
2025-11-25 22:34:49,785 - clink.registry - INFO - Overriding CLI configuration for 'codex' from C:\Users\kadir\.zen\cli_clients\codex.json
2025-11-25 22:34:49,786 - clink.registry - INFO - Overriding CLI configuration for 'gemini' from C:\Users\kadir\.zen\cli_clients\gemini.json
2025-11-25 22:34:49,786 - __main__ - INFO - Active tools: ['apilookup', 'challenge', 'chat', 'clink', 'codereview', 'consensus', 'debug', 'listmodels', 'planner', 'precommit', 'thinkdeep', 'version']
2025-11-25 22:34:49,788 - __main__ - INFO - Custom API endpoint found: http://localhost:11434/v1 with model clink-placeholder
2025-11-25 22:34:49,788 - __main__ - INFO - Registered providers: custom
2025-11-25 22:34:49,788 - __main__ - INFO - Available providers: Custom API (http://localhost:11434/v1)
2025-11-25 22:34:49,788 - __main__ - INFO - Zen MCP Server starting up...
2025-11-25 22:34:49,788 - __main__ - INFO - Available tools: ['chat', 'clink', 'thinkdeep', 'planner', 'consensus', 'codereview', 'precommit', 'debug', 'challenge', 'apilookup', 'listmodels', 'version']
2025-11-25 22:34:49,788 - __main__ - INFO - Server ready - waiting for tool requests...
```

---

*Documentation generated by Claude Code (Opus 4.5) on 2025-11-25*
