---
description: Delegate a task to OpenCode CLI agent for autonomous execution
---

# Use OpenCode Orchestrator

Trigger this workflow to hand off a complex coding task to OpenCode.

## Usage

When invoked, follow these steps:

1. **Ask the user for task details** (if not already provided):
   - What is the task to be completed?
   - Which files or directories are involved?
   - Any specific constraints or requirements?

2. **Read the skill instructions**:
   // turbo
   ```bash
   cat ~/.gemini/antigravity/global_skills/opencode-orchestrator/SKILL.md
   ```
      or in some verisons:
   ```bash
   cat ~/.agent/skills/opencode-orchestrator/SKILL.md
   ```
   Or if using workspace skills:
   ```bash
   cat .agent/skills/opencode-orchestrator/SKILL.md
   ```

3. **Construct the prompt** using the structured template from the skill:
   ```
   <context>
   Project: [from user]
   Working Directory: [absolute path]
   Related Files: [file list]
   </context>

   <task>
   [user's task description]
   </task>

   <requirements>
   [specific requirements]
   </requirements>

   <success_criteria>
   [how to verify completion]
   </success_criteria>
   ```

4. **Execute the handoff**:
   ```bash
   opencode run "YOUR_CONSTRUCTED_PROMPT"
   ```

5. **Monitor and report** the results back to the user.

## Quick Examples

```bash
# Basic task
opencode run "Refactor /path/to/file.ts to use async/await"

# With specific model
opencode run -m github-copilot/claude-sonnet-4 "Complex refactoring task..."

# With plan mode
opencode run --plan "Large codebase change..."
```

## Notes
- OpenCode does NOT share Antigravity's context—include all relevant info in the prompt
- Use `-m` flag to specify model if default is unavailable
- Check `opencode models` for available models
