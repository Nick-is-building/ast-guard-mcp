# ast-guard-mcp

MCP server for [ast-guard](https://github.com/Nick-is-building/ast-guard) — deterministic reward hacking detection for LLM-generated Python code.

## What it does

Exposes ast-guard's static analysis as MCP tools that any compatible agent can call:

- **`ast_guard_scan`** — Compare original vs. generated Python code and detect structural cheating patterns (hardcoded lookups, complexity collapse, forbidden calls, obfuscation, import drift).
- **`ast_guard_feedback`** — Submit feedback on scan results to improve detection thresholds.

No LLM in the loop. Pure AST analysis. Zero latency. Fully deterministic.

## Install

```bash
pip install ast-guard-mcp
```

Or from source:

```bash
git clone https://github.com/Nick-is-building/ast-guard-mcp.git
cd ast-guard-mcp
pip install -e .
```

## Usage

### Claude Code

Add to your Claude Code MCP settings (`~/.claude/settings.json`):

```json
{
  "mcpServers": {
    "ast-guard": {
      "command": "ast-guard-mcp",
      "type": "stdio"
    }
  }
}
```

### Cursor

Add to your Cursor MCP config (`.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "ast-guard": {
      "command": "ast-guard-mcp",
      "type": "stdio"
    }
  }
}
```

### Direct

```bash
ast-guard-mcp
```

## Tools

### `ast_guard_scan`

| Parameter | Type | Default | Description |
|---|---|---|---|
| `original_code` | string | required | Original Python source code |
| `generated_code` | string | required | LLM-generated Python source code |
| `mode` | string | `"strict"` | `"strict"`, `"standard"`, or `"audit"` |

**Returns** a dict with `verdict` (`CLEAN` / `WARNING` / `CRITICAL`), per-check results, detected allowlist transformations, and anonymized telemetry.

### `ast_guard_feedback`

| Parameter | Type | Description |
|---|---|---|
| `scan_id` | string | The scan_id from a previous scan result |
| `label` | string | `"true_positive"`, `"false_positive"`, `"true_negative"`, `"false_negative"` |

## How it fits together

```
Agent writes Python code
         ↓
  MCP call: ast_guard_scan(original, generated)
         ↓
  ast-guard parses both via AST
         ↓
  ┌─────────────┬──────────────┬────────────┐
  │   CLEAN     │   WARNING    │  CRITICAL  │
  │  proceed    │  add context │   block    │
  └─────────────┴──────────────┴────────────┘
```

## Why MCP

- **Universal**: Works with Claude Code, Cursor, Codex, OpenCode — any agent that speaks MCP.
- **No shell-out**: Native tool call instead of `execSync` or subprocess hacks.
- **Composable**: Combine with [FailProofAI](https://github.com/FailproofAI/failproofai) policies, other MCP servers, or standalone.

## Related

- [ast-guard](https://github.com/Nick-is-building/ast-guard) — The core detection engine.
- [FailProofAI integration](https://github.com/FailproofAI/failproofai/issues/375) — Custom policy proposal.

## License

MIT

---

Built by Nick — Deterministic System Builder, Berlin

