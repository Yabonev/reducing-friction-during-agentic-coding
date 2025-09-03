# Reducing Friction During Agentic Coding

Examples and demos for the "Reducing Friction During Agentic Coding" presentation.

## Assumptions

These tips assume:

- **Learning-focused:** You're building for exploration and learning, not mission-critical systems
- **Solo development:** Working alone, not in a team environment
- **Experimental freedom:** Goal is understanding technology while building what interests you
- **Adaptable principles:** If working in teams or production, these techniques need adaptation

## Claude Code Configuration

Claude Code uses a `.claude/` folder for configuration:

- **Global:** `~/.claude/settings.json` - applies to all projects
- **Project:** `./.claude/settings.json` - project-specific overrides
- **Commands:** `./.claude/commands/` - custom slash commands
- **Agents:** `./.claude/agents/` - specialized task agents

Key settings: hooks, permissions, model preferences.

---

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

**Use case 1:** Git commit slash command
Since LLMs will be writing your commit messages, it makes sense to ensure they provide as much useful context as possible for future reference.

```markdown
# Commit Changes

You are tasked with creating git commits for the changes made during this session.

## Process:

1. **Think about what changed:**

   - Review the conversation history and understand what was accomplished
   - Run `git status` to see current changes
   - Run `git diff` to understand the modifications
   - Consider whether changes should be one commit or multiple logical commits

2. **Plan your commit(s):**

   - Identify which files belong together
   - Draft clear, descriptive commit messages
   - Use imperative mood in commit messages
   - Focus on why the changes were made, not just what

3. **Present your plan to the user:**

   - List the files you plan to add for each commit
   - Show the commit message(s) you'll use
   - Ask: "I plan to create [N] commit(s) with these changes. Shall I proceed?"

4. **Execute upon confirmation:**
   - Use `git add` with specific files (never use `-A` or `.`)
   - Create commits with your planned messages
   - Push each commit immediately with `git push`
   - Show the result with `git log --oneline -n [number]`

## Important:

- **NEVER add co-author information or Claude attribution**
- Commits should be authored solely by the user
- Do not include any "Generated with Claude" messages
- Do not add "Co-Authored-By" lines
- Write commit messages as if the user wrote them

## Remember:

- You have the full context of what was done in this session
- Group related changes together
- Keep commits focused and atomic when possible
- The user trusts your judgment - they asked you to commit
```

**Use case 2:** Progress snapshot for context switching

```markdown
# Progress Snapshot

You are tasked with creating a comprehensive progress snapshot of this coding session.

## Process:

1. **Analyze the session:**

   - Review the entire conversation history from start to current point
   - Identify the original goals and objectives stated by the user
   - List all files that were read, modified, created, or discovered (e.g., `auth.py` was modified, `config.json` was read, `new-feature.tsx` was created)
   - Record any research performed (e.g., "reviewed Express.js documentation", "searched Stack Overflow for authentication patterns")

2. **Categorize the work:**

   - List completed tasks and their outcomes (e.g., "fixed login validation bug", "implemented JWT token refresh")
   - List remaining tasks or blockers (e.g., "need to add error handling for expired tokens", "blocked on database schema approval")
   - Document failed attempts with reasoning (e.g., "tried using bcrypt for passwords but caused 2s delay on login", "attempted Redis clustering but configuration conflicts with existing setup")
   - Record key insights, patterns, or discoveries (e.g., "current auth system doesn't handle concurrent sessions", "performance bottleneck in user lookup query")
   - Record architectural decisions made (e.g., "chose Redis for session storage", "decided to use middleware pattern for auth")

3. **Structure the output:**

   - Use the format below for consistency
   - Focus ONLY on actionable facts and current state
   - Eliminate fluff, explanations, or commentary
   - Prioritize information useful for future sessions

4. **Present the snapshot:**
   - Show structured summary using the template
   - Highlight critical blockers or next steps
   - Include file paths and specific technical details

## Output Format:

## Session: [Project/Feature Name]

**Original Goal:** [What user wanted to accomplish]  
**Current Status:** [In progress/Blocked/Nearly complete/etc.]

**Files Examined:** [file1.js:45-67, config.json, src/components/]  
**Files Modified:** [auth.py, database.sql, package.json]  
**Files Created:** [new-feature.tsx, migration-001.sql]

**Research Performed:** [API documentation reviewed, Stack Overflow solutions, GitHub issues]  
**External Resources:** [Specific URLs, documentation links, references]

**Tasks Completed:**

- [Specific accomplishment with technical detail]
- [Another completed task with outcome]

**Tasks Remaining:**

- [Next actionable item with context]
- [Another pending task with priority]

**Failed Attempts:**

- [What was tried and failed with reasoning why it failed]
- [Another approach tested that didn't work and lessons learned]

**Key Insights:**

- [Important technical discovery]
- [Architecture decision or constraint]
- [Performance consideration or limitation]

**Blockers:** [Current impediments to progress]  
**Next Session Priority:** [Most important immediate action]

## Remember:

- Be factual and specific - avoid generalizations
- Include file paths, line numbers, function names where relevant
- Focus on information that helps resume work efficiently
- The user trusts you to capture everything important

## Verification Requirements:

Before claiming any functionality works or making technical assertions:

1. **Evidence Check:** What concrete evidence do I have that this actually works?
2. **Distinguish Facts vs Assumptions:** Separate confirmed observations from assumptions
3. **Verification Status:** Mark claims as "Confirmed by [testing/observation]" or "Requires verification"
4. **Claim Calibration:** Adjust statements to match actual evidence level
5. **Avoid Assumption Cascade:** Don't build new claims on unverified assumptions
```

**Adding a new command:**

1. **Project-level:** Create `.claude/commands/commit.md` in your current project
   **OR**
   **User-level:** Create `~/.claude/commands/commit.md` for all projects

2. Define the structured process you want Claude to follow in the markdown file

3. **Restart Claude Code** to make the new command available

4. Use `/commit` instead of typing the same instructions repeatedly

_Source for the prompt_: [HumanLayer's commit command](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/commit.md)  
_Documentation_: [Claude Code slash commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)

_Note_: You can customize slash commands by providing metadata in the prompt, I do that only if needed, this let's me migrate away from CC if better tool shows up.

---

### Tip 5: Chain slash commands and subagents for complex workflows

**The Concept:** Create specialized commands that work together - the output of one becomes the input of another.

> [!TIP] > **Principle:** Specialized commands should work together - the output of one should be the input of another when that makes sense.

**Example:**

1. `/research` → gathers information about a topic with a specific output template
2. `/analyze` → takes the specific research output and extracts key insights because it has specific instructions how to handle this template

---

### Tip 6: Use precise verbs with examples when creating commands

**Command Creation Template:**

```markdown
# [Command Name]

## Persona

You are a [specific role with clear expertise area].

## Task

[Single, clear objective - avoid compound tasks]

## Process

1. **[Specific Verb]:** [What this verb means] (e.g., "[concrete example]")
2. **[Another Verb]:** [What this verb means] (e.g., "[concrete example]")
3. **[Final Verb]:** [What this verb means] (e.g., "[concrete example]")

## Output Format

[Exact structure expected]

**[Any other relevant information given the task and context you've processed]**

## Constraints

- [Specific limitation]
- [Another limitation]
- [Performance/quality requirement]

## Examples

**Input:** [Sample input]
**Expected Output:** [Sample output]
```

**Verb Precision Examples:**

- ❌ "Track files"
- ✅ "List all files that were read, modified, or created (e.g., `auth.py` was modified, `config.json` was read)"

- ❌ "Capture insights"
- ✅ "Record key discoveries with specific details (e.g., 'performance bottleneck in user lookup query at line 45')"

---

### Tip 7: Provide the right context given the available tools

> [!TIP] > **Principle:** When providing context, format it so the LLM can use its available tools to one-shot access that exact context.

**Examples:**

❌ **Bad:** "The authentication logic in `auth.py` handles user validation"

✅ **Good:** "The authentication logic in `auth.py:45-52` validates users:

```python
def validate_user(username, password):
    user = User.query.filter_by(username=username).first()
    if user and user.check_password(password):
        return user
    return None
```

❌ **Bad:** "Error handling in `api/handlers.py` catches validation errors"

✅ **Good:** "Error handling in `api/handlers.py:123-130` catches validation errors:

```python
try:
    result = process_request(data)
except ValidationError as e:
    return jsonify({'error': str(e)}), 400
```

**Context Engineering Patterns:**

- **File References:** `auth.py:45-52` (Read tool can access exact lines)
- **Function References:** `validateUser()` in `src/auth/handlers.py` (Grep tool can find immediately)
- **Error Messages:** `"ValidationError: Invalid password format"` (Grep can locate exact occurrence)
- **URL Paths:** `/api/v1/users/login` (Grep can find route definitions)
- **Repetition leads to attention**: Reading code snippet and then finding that same code in the file system strengthens the focus on those specific parts of the code.

> [!TIP]
> Repetition is good, when you repeat the correct thing.

---

### Tip 8: Use subagents for context isolation, not for creating friends

**The Mindset:** Create subagents when you want to execute a task outside the main agent's context without polluting it with research artifacts.

**Use Cases:**

- **Web research:** Fetch and analyze multiple sources without filling main context with URLs and content
- **Codebase exploration:** Deep dive into unfamiliar code without bloating main conversation with file contents
- **Bug investigation:** Trace through logs, stack traces, and error patterns in isolation

**When NOT to use subagents:**

- **Interactive discussions:** If you feel the need to ask some questions after the execution happens, you probably should use slash commands in separate context windows instead

**Implementation:**

- Use default subagent creation (Task tool with appropriate agent type)
- Give clear, specific objectives to the subagent
- **Be explicit about output format:** Define exactly what information should flow between agents
- **Always include flexibility:** Add `[Any other relevant information given the task and context]` to templates
- Give the least amount of tools access as possible for the task

**Example Output Template:**

```markdown
## Research Results: [Topic]

**Key Findings:**

- [Finding 1 with source]
- [Finding 2 with source]

**Recommendations:**

- [Actionable recommendation 1]
- [Actionable recommendation 2]

**Blockers/Concerns:**

- [Issue 1]
- [Issue 2]

**[Any other relevant information given the research context and sources analyzed]**
**[Any other relevant information to further research that might be missing from this research]**
```

---

### Tip 9: Prefer programmatically blocking execution over prompting for it

**The Principle:** Instead of instructing "always use X instead of Y", configure your environment to automatically prevent incorrect commands and guide the LLM.

**Example:** Always using `uv` when working with python https://pydevtools.com/blog/interceptors/

**LLM Self-Correction in Action:**

````bash
$ python main.py
❌ Direct python usage detected!
🤖 AI Agent Instructions: Instead of 'python main.py', use: uv run main.py

$ uv run main.py
✅ Running main.py with uv...
```bash
$ pip install -r requirements.txt
❌ Direct pip usage detected!
🤖 AI Agent Instructions: Instead of 'pip install -r requirements.txt', use: uv pip sync requirements.txt

$ uv pip sync requirements.txt
✅ Syncing dependencies with uv...
````

---

### Tip 10: Block direct pushes and enforce CI checks over prompting

**The Principle:** Instead of instructing "always run tests and linting before committing", configure your repository to automatically enforce these checks.

**Enable branch protection:**

```bash
echo '{
  "required_status_checks": {
    "strict": true,
    "contexts": []
  },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "required_approving_review_count": 1
  },
  "restrictions": null
}' | gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --input -
```

**Test the blocking:**

```bash
git commit -m "Test commit"
git push origin main
```

```
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: - Changes must be made through a pull request.
error: failed to push some refs
```

**Remove protection (if needed):**

```bash
gh api repos/:owner/:repo/branches/main/protection --method DELETE
```

**CI Pipeline:**

```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
    branches: [main]

jobs:
  quality-checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Random quality check
        run: |
          # 50% chance of failure to demo blocking
          if [ $((RANDOM % 2)) -eq 0 ]; then
            echo "❌ Quality check failed"
            exit 1
          else
            echo "✅ Quality check passed"
          fi
```

## Example Development Workflow

- **Feature Overview + Branch:** `cc "Add user authentication"` → `git checkout -b feature/user-auth`
- **Plan & Research:** `cc "/research auth patterns"` → Write detailed plan → Iterate until approved
- **Clear Context:** `cc "/progress"` → Fresh conversation
- **Implement:** Focus purely on coding per plan
- **Test + PR:** Unit tests → User testing → `cc "/commit"` → Create PR → Merge

---

# Tips/ techniques I haven't found that useful

- git worktrees (I feel I need more time mastering one execution flow before going bombastic on the same branch with different but similar tasks)

```

```
