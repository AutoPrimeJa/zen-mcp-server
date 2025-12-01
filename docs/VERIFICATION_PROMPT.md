# AI Verification Agent Prompt

## Purpose
This prompt is designed for an AI agent to independently verify that the Meta-Orchestrator system was correctly installed and configured. The verifying agent should treat all claims as potentially false until proven through direct evidence.

---

## VERIFICATION PROMPT

Copy and paste the following prompt to a new AI agent (Claude Code, or similar):

---

```
You are a VERIFICATION AGENT. Your task is to audit and verify claims made about a "Meta-Orchestrator" system installation. You must be skeptical and verify each claim through direct evidence. Do NOT trust documentation alone - execute commands and read files to confirm.

## Context
A previous AI agent claimed to have set up the following:
1. Zen MCP Server cloned and configured
2. OpenAI Codex CLI installed and authenticated
3. Google Gemini CLI installed and authenticated
4. Custom "clink" bridge configurations for worker/researcher roles
5. MCP server registered with Claude Code

## Your Mission
Execute the verification checklist below. For each item:
- Run the specified command or read the specified file
- Compare the actual result to the expected result
- Mark as ✓ VERIFIED, ✗ FAILED, or ⚠ PARTIAL
- Document any discrepancies

## VERIFICATION CHECKLIST

### Section 1: Prerequisites (Run these commands)

1.1 Node.js Installation
- Command: `node --version`
- Expected: Version 24.x.x or higher
- Status: [ ]

1.2 npm Installation
- Command: `npm --version`
- Expected: Version 11.x.x or higher
- Status: [ ]

1.3 Python Installation
- Command: `python --version`
- Expected: Version 3.14.x or compatible
- Status: [ ]

### Section 2: CLI Tools (Run these commands)

2.1 Codex CLI Installed
- Command: `codex --version`
- Expected: Version 0.63.0 or similar
- Status: [ ]

2.2 Gemini CLI Installed
- Command: `gemini --version`
- Expected: Version 0.17.x or similar
- Status: [ ]

2.3 Codex Authentication Status
- Command: `codex login status`
- Expected: "Logged in using ChatGPT" or similar authenticated message
- Status: [ ]

2.4 Gemini Authentication Status
- Command: `gemini auth status`
- Expected: "Loaded cached credentials" or similar authenticated message
- Status: [ ]

### Section 3: Zen MCP Server (Read files and run commands)

3.1 Repository Exists
- Command: `ls C:/Users/kadir/zen-orchestrator/`
- Expected: Should list server.py, requirements.txt, clink/, conf/, etc.
- Status: [ ]

3.2 Server.py Exists and is Valid
- Read file: `C:/Users/kadir/zen-orchestrator/server.py`
- Expected: Python file with MCP server implementation, should contain "configure_providers" function
- Status: [ ]

3.3 Requirements Installed (Check for key package)
- Command: `pip show mcp`
- Expected: Shows mcp package info (version 1.x.x)
- Status: [ ]

3.4 Environment File Exists
- Read file: `C:/Users/kadir/zen-orchestrator/.env`
- Expected: Contains CUSTOM_API_URL, DEFAULT_MODEL, LOG_LEVEL settings
- Status: [ ]

3.5 Server Can Start
- Command: `cd C:/Users/kadir/zen-orchestrator && timeout 5 python server.py 2>&1`
- Expected: Logs showing "Server ready - waiting for tool requests"
- Status: [ ]

### Section 4: Clink Configuration (Read files)

4.1 User Config Directory Exists
- Command: `ls C:/Users/kadir/.zen/cli_clients/`
- Expected: Lists codex.json and gemini.json
- Status: [ ]

4.2 Codex Config Has Worker Role
- Read file: `C:/Users/kadir/.zen/cli_clients/codex.json`
- Expected: JSON with "worker" role defined, pointing to worker.txt prompt
- Status: [ ]

4.3 Gemini Config Has Researcher Role
- Read file: `C:/Users/kadir/.zen/cli_clients/gemini.json`
- Expected: JSON with "researcher" role defined, pointing to researcher.txt prompt
- Status: [ ]

4.4 Worker Prompt File Exists
- Read file: `C:/Users/kadir/zen-orchestrator/systemprompts/clink/worker.txt`
- Expected: Text file with worker agent instructions
- Status: [ ]

4.5 Researcher Prompt File Exists
- Read file: `C:/Users/kadir/zen-orchestrator/systemprompts/clink/researcher.txt`
- Expected: Text file with researcher agent instructions
- Status: [ ]

### Section 5: MCP Registration (Run commands)

5.1 MCP Server Registered
- Command: `claude mcp list`
- Expected: Shows "zen" server with python command
- Status: [ ]

5.2 MCP Server Connected
- Command: `claude mcp list`
- Expected: Shows "✓ Connected" status for zen server
- Status: [ ]

5.3 Claude Config Updated
- Read file: `C:/Users/kadir/.claude.json`
- Expected: Contains mcpServers.zen configuration
- Status: [ ]

### Section 6: Integration Verification

6.1 Built-in Configs Exist
- Command: `ls C:/Users/kadir/zen-orchestrator/conf/cli_clients/`
- Expected: Lists claude.json, codex.json, gemini.json
- Status: [ ]

6.2 Registry Loads Custom Configs
- Read server startup logs for: "Overriding CLI configuration for 'codex'"
- Expected: Logs show user configs override built-in configs
- Status: [ ]

6.3 Clink Tool Available
- From server startup logs, check active tools
- Expected: 'clink' should be in the active tools list
- Status: [ ]

## OUTPUT FORMAT

After completing all verifications, provide a summary report:

```
## VERIFICATION REPORT
Date: [Current Date]
Verifier: [Your Model Name]

### Summary
- Total Checks: 19
- Verified: X
- Failed: Y
- Partial: Z

### Detailed Results

#### Section 1: Prerequisites
1.1 Node.js: [STATUS] - [Actual output]
1.2 npm: [STATUS] - [Actual output]
1.3 Python: [STATUS] - [Actual output]

[Continue for all sections...]

### Critical Issues Found
[List any FAILED items that would prevent the system from working]

### Recommendations
[List any fixes or improvements needed]

### Final Verdict
[ ] FULLY OPERATIONAL - All checks passed
[ ] PARTIALLY OPERATIONAL - Some non-critical issues
[ ] NOT OPERATIONAL - Critical failures found
```

## Important Notes for Verifier

1. **Do not trust documentation** - Always verify by running commands or reading actual files
2. **Check file contents** - Don't just check if files exist; verify they contain expected content
3. **Test actual functionality** - The server starting is more important than config file existence
4. **Report exact outputs** - Include actual command outputs in your report
5. **Be thorough** - A false positive (saying something works when it doesn't) is worse than a false negative

## If Verification Fails

If critical components fail verification:
1. Document exactly what failed and why
2. Check if the failure is due to:
   - Missing installation step
   - Incorrect configuration
   - Authentication expired
   - Path/permission issues
3. Do NOT attempt to fix issues unless explicitly asked
4. Report findings to the user for remediation

---

END OF VERIFICATION PROMPT
```

---

## Quick Verification Commands

For manual verification, run these commands in sequence:

```bash
# Prerequisites
node --version
npm --version
python --version

# CLI Tools
codex --version
gemini --version
codex login status
gemini auth status

# Zen MCP Server
ls C:/Users/kadir/zen-orchestrator/
cat C:/Users/kadir/zen-orchestrator/.env
pip show mcp

# Clink Configs
ls C:/Users/kadir/.zen/cli_clients/
cat C:/Users/kadir/.zen/cli_clients/codex.json
cat C:/Users/kadir/.zen/cli_clients/gemini.json

# MCP Status
claude mcp list
```

---

## Expected Healthy State

When everything is working correctly:

1. **Server startup shows:**
   - "Overriding CLI configuration for 'codex'"
   - "Overriding CLI configuration for 'gemini'"
   - "Active tools: [..., 'clink', ...]"
   - "Server ready - waiting for tool requests"

2. **`claude mcp list` shows:**
   - "zen: python C:/Users/kadir/zen-orchestrator/server.py - ✓ Connected"

3. **Authentication status:**
   - Codex: "Logged in using ChatGPT"
   - Gemini: "Loaded cached credentials"

---

*Verification prompt created 2025-11-25*
