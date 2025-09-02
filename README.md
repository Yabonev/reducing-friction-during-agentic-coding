# Reducing Friction During Agentic Coding

Examples and demos for the "Reducing Friction During Agentic Coding" presentation.

## PART I: Reducing Friction User to LLM

This section is about how you can provide more context, better context, faster by having tighter feedback loops between you and CC.

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

### Tip 3: Use Speech-to-Text

**Tool Recommendations:**

- **Free:** [ChatGPT Desktop app](https://openai.com/chatgpt/desktop/) (Nicholas's tip)
- **Freemium:** [SuperWhisper](https://superwhisper.com/), what I am using, free local version works well

**Why It Works:**

- **Less Screen Time:** We got too much screen time anyway
- **Recording Pressure:** The recording bubble can force you to begin and continue talking - no stopping mid-thought, repetition is okay, this is part of the thinking process
- **Emphasis Through Repetition:** Repeating the same information or instructions using different words actually focuses the LLM better
- **Important Stuff Last:** You can naturally finish by emphasizing most important parts, you've spend the effort of talking so much anyway

**Personal Use Cases:**

- **Note-taking and brainstorming**, I've spend around 3 hours talking about this content to an LLM to generate the content notes
- Situations where my output/thinking is the main focus
- Less useful for pure coding, more for planning and ideation, explaining features, requirements

> [!TIP]
> You will be judged by others for talking to AI. Accept it :thumbsup:

---

### Tip 4: Create slash commands for repetitive tasks

**Think of it as:** Reusable prompts that you can trigger with `/command-name` in the chat with CC.

Executed in the same context as the current conversation.

**Use case 1:** (Git commit slash command)[./prompts/commit.md]
Since LLMs will be writing your commit messages, it makes sense to ensure they provide as much useful context as possible for future reference.

**How it Works:**

1. **Project-level:** Create `.claude/commands/commit.md` in your current project
   **OR**
   **User-level:** Create `~/.claude/commands/commit.md` for all projects

2. Define the structured process you want Claude to follow in the markdown file

3. **Restart Claude Code** to make the new command available

4. Use `/commit` instead of typing the same instructions repeatedly

_Source for the prompt_: [HumanLayer's commit command](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/commit.md)  
_Documentation_: [Claude Code slash commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)

_Note_: You can customize slash commands by providing metadata in the prompt, I do that only if needed, this let's me migrate away from CC if better tool shows up.

# Tips/ techniques I haven't found that useful

- git worktrees (I feel I need more time mastering one execution flow before going bombastic on the same branch with different but similar tasks)
