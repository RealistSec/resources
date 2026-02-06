---
description: Delegate a task to OpenCode with Oh My OpenCode agents (Sisyphus, Oracle, Librarian, etc.)
---

# Use OMO OpenCode Orchestrator

Trigger this workflow to hand off a task to OpenCode enhanced with Oh My OpenCode's curated agents.

## Usage

When invoked, follow these steps:

1. **Ask the user for task details** (if not already provided):
   - What is the task to be completed?
   - Which agent is best suited? (or use ultrawork for auto-selection) 
   - Any specific constraints or requirements?

2. **Read the skill instructions**:
   // turbo
   cat ~/.gemini/antigravity/global_skills/omo-opencode-orchestrator/SKILL.md
   ```
   or in some Linux AntiGravity verisons:
   ```bash
   cat ~/.agent/skills/omo-opencode-orchestrator/SKILL.md
   ```
   Or if using workspace skills:
   ```bash
   cat .agent/skills/omo-opencode-orchestrator/SKILL.md
   

3. **Select the appropriate agent**:

   | Task Type | Agent | Command |
   |-----------|-------|---------|
   | Complex multi-step | Sisyphus (default) | `opencode run "..."` |
   | Auto-orchestration | Ultrawork | `opencode run "ulw: ..."` |
   | Architecture/debugging | Oracle | `opencode run -a oracle "..."` |
   | Frontend/UI | Frontend | `opencode run -a frontend "..."` |
   | Docs/research | Librarian | `opencode run -a librarian "..."` |
   | Fast code search | Explore | `opencode run -a explore "..."` |

4. **Construct the prompt** using the structured template:
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

5. **Execute the handoff**:
   ```bash
   opencode run "YOUR_CONSTRUCTED_PROMPT"
   # OR for automatic orchestration:
   opencode run "ulw: YOUR_TASK_DESCRIPTION"
   ```

6. **Monitor and report** the results back to the user.

## Quick Examples

```bash
# Ultrawork mode (recommended - auto-orchestrates agents)
opencode run "ulw: Refactor authentication to use JWT"

# Specific agents
opencode run -a oracle "Debug this infinite loop in /path/to/file.ts"
opencode run -a frontend "Create responsive dashboard component"
opencode run -a librarian "Research Hono.js middleware patterns"
opencode run -a explore "Find all usages of deprecated API"
```

## Notes
- Use `ulw:` prefix for automatic agent orchestration
- OpenCode does NOT share Antigravity's context—include all info in prompt
- OMO includes built-in MCPs: Exa (web search), Context7 (docs), grep.app (GitHub search)
