# Reducing Friction During Agentic Coding

Examples and demos for the "Reducing Friction During Agentic Coding" presentation.

## Reducing Friction in Communication: User to LLM and in Reverse

### Permission Bypass Alias

```bash
alias cc='claude --dangerously-bypass-permissions'
```

The `--dangerously-bypass-permissions` flag eliminates permission requests for tool calls. Claude Code executes read, write, bash commands and so on without requesting explicit approval for each action.

### Design Choice

Claude Code defaults to asking for permission. Opposed to OpenCode, which operates on "permitted by default, deny explicitly"

> [!TIP]
> When starting with Claude Code, run normally first to get a feel for how it works before using the bypass flag.

> [!WARNING]
> Never use `--dangerously-bypass-permissions` if you care about what you're exposing. NEVER give it access to production databases or sensitive systems.

---
