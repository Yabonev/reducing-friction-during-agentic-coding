# Reducing Friction During Agentic Coding

Examples and demos for the "Reducing Friction During Agentic Coding" presentation.

## Reducing Friction in Communication: User to LLM and in Reverse

### Tip 1: Give tool calls permission by default

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

### Tip 2: Use "Stop" event hook to get a notification when Claude Code finishes execution

MacOS hook configuration in `~/.claude/settings.json`:

```json
"hooks": {
  "Stop": [
    {
      "hooks": [
        {
          "type": "command",
          "command": "afplay /System/Library/Sounds/Glass.aiff"
        }
      ]
    }
  ]
}
```

This plays the Glass system sound when Claude finishes processing. [Official Docs to Hooks](https://docs.anthropic.com/en/docs/claude-code/hooks)

> [!TIP]
> The "Glass.aiff" sound is **superior** to other system sounds on MacOS. Prove me wrong.

---
