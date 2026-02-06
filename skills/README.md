# Antigravity Skills Collection

A curated collection of skills for [Google Antigravity](https://antigravity.google)—the agentic development platform.

## What are Skills?

Skills are reusable packages of knowledge that extend what Antigravity agents can accomplish. 
Each skill contains:
- **SKILL.md** — Instructions for the agent on how to approach a specific type of task
- Optional workflows, scripts, templates, or resources

When you start a conversation, Antigravity scans available skills and automatically uses relevant ones based on your task.

---

## Available Skills

| Skill | Workflow | Description |
|-------|----------|-------------|
| [opencode-orchestrator](./opencode-orchestrator/) | `/use-opencode` | Delegate complex coding tasks to OpenCode CLI agent |
| [omo-opencode-orchestrator](./omo-opencode-orchestrator/) | `/use-omo-opencode` | OpenCode + Oh My OpenCode agents (Sisyphus, Oracle, Librarian, etc.) |

---

## Quick Start

### Using Workflows (Recommended)
Trigger a skill with its slash command:
```
/use-opencode
/use-omo-opencode
```

### Automatic Discovery
Just describe your task—Antigravity will detect and use relevant skills automatically.

### Explicit Reference
Mention a skill by name: *"Use the opencode-orchestrator skill to refactor my codebase"*

---

## Installation

### Global Skills (All Projects)
Copy skill folders to your global skills directory:
```bash
cp -r skills/* ~/.gemini/antigravity/global_skills/
```

Copy workflows to your global workflows directory:
```bash
cp skills/opencode-orchestrator/use-opencode.md ~/.gemini/antigravity/global_workflows/
cp skills/omo-opencode-orchestrator/use-omo-opencode.md ~/.gemini/antigravity/global_workflows/
```

### Workspace Skills (Single Project)
Copy skill folders to your project's skills directory:
```bash
cp -r skills/* /path/to/project/.agent/skills/
```

---

## Skill Details

### opencode-orchestrator
Delegates complex multi-step coding tasks to [OpenCode](https://opencode.ai), a terminal-based autonomous AI agent.

| Trigger | `/use-opencode` |
|---------|-----------------|
| Best for | Multi-file refactoring, long-running tasks, deep codebase exploration |

### omo-opencode-orchestrator
Enhanced version for [Oh My OpenCode](https://github.com/code-yeongyu/oh-my-opencode) users with pre-configured agents:

| Trigger | `/use-omo-opencode` |
|---------|---------------------|
| Magic keyword | `ultrawork` or `ulw` for auto-orchestration |

| Agent | Specialty |
|-------|-----------|
| **Sisyphus** | Main orchestrator (Opus 4.5) |
| **Oracle** | Architecture & debugging (GPT 5.2) |
| **Frontend** | UI/UX development (Gemini 3 Pro) |
| **Librarian** | Documentation research (Claude Sonnet 4.5) |
| **Explore** | Fast codebase grep (Grok Code) |

---

## License

MIT
