# Meta-Orchestrator Quick Start Guide

## What You Have

A **CLI-Link (clink) orchestration system** that allows Claude Code to delegate tasks to:
- **Codex CLI** (OpenAI) - Worker role for code implementation
- **Gemini CLI** (Google) - Researcher role for analysis and research

## Quick Status Check

```bash
# Verify MCP server is connected
claude mcp list
# Expected: zen: ... - ✓ Connected

# Verify CLI authentications
codex login status
# Expected: Logged in using ChatGPT

gemini auth status
# Expected: Loaded cached credentials
```

## Using the System

After restarting Claude Code, the `clink` MCP tool is available.

### Example: Research Task (Gemini)
Ask Claude:
```
Use the clink tool with cli=gemini and role=researcher to research
the latest patterns for React state management in 2025.
```

### Example: Implementation Task (Codex)
Ask Claude:
```
Use the clink tool with cli=codex and role=worker to implement
a TypeScript utility function that debounces API calls.
```

### Example: Multi-Agent Workflow
```
1. First, use clink with gemini/researcher to analyze best practices for X
2. Then use clink with codex/worker to implement based on the research
3. Finally, I (Claude) will review and suggest improvements
```

## Available Roles

| CLI | Role | Purpose |
|-----|------|---------|
| codex | worker | Code implementation |
| codex | default | General tasks |
| codex | planner | Architecture planning |
| codex | codereviewer | Code review |
| gemini | researcher | Research & analysis |
| gemini | default | General tasks |
| gemini | planner | Architecture planning |
| gemini | codereviewer | Code review |

## Troubleshooting

### MCP Server Not Connected
```bash
# Check server manually
cd C:/Users/kadir/zen-orchestrator
python server.py
```

### Authentication Expired
```bash
# Re-authenticate Codex
codex login

# Re-authenticate Gemini
gemini auth login
```

### View Logs
```bash
cat C:/Users/kadir/zen-orchestrator/logs/mcp_server.log
```

## Key Files

| File | Purpose |
|------|---------|
| `C:\Users\kadir\zen-orchestrator\.env` | Server environment config |
| `C:\Users\kadir\.zen\cli_clients\codex.json` | Codex CLI roles |
| `C:\Users\kadir\.zen\cli_clients\gemini.json` | Gemini CLI roles |
| `C:\Users\kadir\.claude.json` | Claude MCP registration |

## Full Documentation

See [META_ORCHESTRATOR_SETUP.md](./META_ORCHESTRATOR_SETUP.md) for complete documentation.

---
*Created 2025-11-25*
