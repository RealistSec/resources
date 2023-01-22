---
name: omo-opencode-orchestrator
description: Delegate complex coding tasks to OpenCode with Oh My OpenCode (OMO) enhancements—pre-configured agents (Sisyphus, Oracle, Librarian, Explore, Frontend), ultrawork mode, and curated MCPs.
---

# OMO OpenCode Orchestrator

<purpose>
This skill enables Antigravity to delegate complex, multi-step coding tasks to OpenCode enhanced with **Oh My OpenCode (OMO)**—a batteries-included plugin providing curated agents, parallel execution, and productivity features. OMO transforms OpenCode into a full development team with specialized agents for different task types.
</purpose>

## Trigger Workflow

Use the `/use-omo-opencode` slash command to invoke this skill:
```
/use-omo-opencode
```
This will guide you through selecting the right agent and constructing an optimal handoff.

## When to Use OMO OpenCode

| Use OMO OpenCode When | Stay in Antigravity When |
|-----------------------|--------------------------|
| Multi-file refactoring across 15+ files | Quick single-file edits |
| Tasks requiring multiple specialized agents | Answering questions about code |
| Parallel agent execution needed | Browser-based testing |
| Deep codebase exploration with Librarian | Tasks needing current conversation context |
| Frontend UI/UX work via Gemini 3 Pro | Interactive debugging with user |

---

## 1. Pre-Installed Agents

<omo_agents>
OMO comes with 5 curated, specialized agents:

| Agent | Model | Specialty | Use For |
|-------|-------|-----------|---------|
| **Sisyphus** | Opus 4.5 High | Main orchestrator | Complex multi-step tasks, orchestration |
| **Oracle** | GPT 5.2 Medium | Architecture & debugging | Design decisions, debugging loops, strategic backup |
| **Frontend** | Gemini 3 Pro | UI/UX development | Frontend components, styling, visual work |
| **Librarian** | Claude Sonnet 4.5 | Documentation & research | Official docs, open source exploration, codebase analysis |
| **Explore** | Grok Code | Fast codebase grep | Blazing fast code search with contextual grep |

### Additional Agents
- **Prometheus**: Planner agent for task breakdown
- **Metis**: Plan consultant for reviewing approaches
- **Multimodal Looker**: Image and visual analysis
</omo_agents>

---

## 2. The Magic Word: `ultrawork`

<ultrawork>
The simplest way to invoke OMO's full capabilities:

```bash
opencode run "ultrawork: Refactor the authentication module to use JWT tokens"
```

Or the even shorter version:
```bash
opencode run "ulw: Refactor the authentication module to use JWT tokens"
```

### What `ultrawork` Enables Automatically
- Parallel agents mapping the codebase in background
- LSP-powered surgical refactoring
- Task delegation to specialized agents
- Todo enforcement (agent continues until complete)
- Comment cleanup (code indistinguishable from human-written)
- Automatic context gathering from docs and source
</ultrawork>

---

## 3. Handoff Protocol

<handoff_instructions>
### Critical: Context Transfer

OpenCode does **NOT** share Antigravity's conversation context. You must explicitly provide:

1. **File Paths**: Absolute paths to all relevant files
2. **Task Context**: Background information and requirements
3. **Constraints**: Any specific approaches to use or avoid
4. **Success Criteria**: Clear definition of done

### Command Structure

```bash
opencode run "YOUR_DETAILED_PROMPT"
```

### Agent-Specific Flags

| Flag | Purpose | Example |
|------|---------|---------|
| `-a sisyphus` | Main orchestrator (default) | Complex multi-step tasks |
| `-a oracle` | Design & debugging | When stuck or need architecture help |
| `-a frontend` | UI/UX development | React/Vue/frontend components |
| `-a librarian` | Documentation research | Explore frameworks, read docs |
| `-a explore` | Fast codebase grep | Quick file/pattern discovery |

</handoff_instructions>

---

## 4. Prompt Construction

<prompt_template>
Structure prompts with clear sections for best results:

```
<context>
Project: [Project name and type]
Working Directory: [Absolute path]
Related Files: [List of relevant file paths]
</context>

<background>
[Any necessary context not obvious from files]
</background>

<task>
[Clear, specific task description]
</task>

<requirements>
- [Specific requirement 1]
- [Specific requirement 2]
</requirements>

<constraints>
- [What to avoid or preserve]
</constraints>

<success_criteria>
- [How to know the task is complete]
</success_criteria>
```

**Pro Tip**: Prefix with `ultrawork:` or `ulw:` for automatic agent orchestration.
</prompt_template>

---

## 5. Example Workflows

<examples>
### Ultrawork Mode (Recommended)
```bash
opencode run "ulw: Refactor /home/user/project/src to use TypeScript strict mode. Update all imports, add proper types, fix any type errors."
```

### Using Specific Agents

#### Oracle for Architecture Decisions
```bash
opencode run -a oracle "
<context>
Working Directory: /home/user/project
Current Architecture: Monolithic Express.js app
</context>

<task>
Analyze whether we should split into microservices or stay monolithic.
Consider our current scale (10k DAU) and team size (3 devs).
</task>

<success_criteria>
- Architecture decision document in ARCHITECTURE.md
- Pros/cons analysis
- Migration steps if recommending change
</success_criteria>
"
```

#### Librarian for Documentation Research
```bash
opencode run -a librarian "
<context>
Project: /home/user/project
Framework: Hono.js
</context>

<task>
Research Hono.js middleware patterns and implement rate limiting.
</task>

<requirements>
- Find official Hono.js middleware documentation
- Check for existing rate limiting implementations
- Implement using best practices from source
</requirements>
"
```

#### Frontend Agent for UI Work
```bash
opencode run -a frontend "
<context>
Working Directory: /home/user/project
Stack: React, Tailwind CSS
</context>

<task>
Create a responsive dashboard layout with sidebar navigation.
</task>

<requirements>
- Mobile-first design
- Dark mode support
- Smooth transitions
</requirements>
"
```

#### Explore for Fast Code Discovery
```bash
opencode run -a explore "
Find all usages of the deprecated UserService.authenticate() method across the codebase at /home/user/project
"
```
</examples>

---

## 6. OMO Features

<omo_features>
### Built-in MCPs
- **Exa**: Web search for current information
- **Context7**: Official documentation lookup
- **grep.app**: GitHub code search across public repos

### Productivity Features
| Feature | Description |
|---------|-------------|
| **Todo Enforcer** | Forces agent to continue until task complete |
| **Comment Checker** | Prevents excessive AI comments |
| **LSP Integration** | Surgical refactoring with rename/diagnostics |
| **AST Tools** | AST-aware code search and modification |
| **Background Agents** | Parallel execution like a real dev team |
| **Session Tools** | List, read, search session history |

### Configuration Locations
```bash
# Project-specific
.opencode/oh-my-opencode.json

# User-wide
~/.config/opencode/oh-my-opencode.json
```
</omo_features>

---

## 7. Integration with Antigravity Workflow

<antigravity_integration>
### Coordinated Handoffs

When delegating from Antigravity to OMO OpenCode:

1. **Choose Agent**: Select appropriate agent for task type
2. **Prepare Context**: Gather all relevant file paths and requirements
3. **Construct Prompt**: Use structured template or `ultrawork` keyword
4. **Execute Handoff**: Run via `run_command` tool
5. **Monitor Output**: Read OpenCode's logs and final result
6. **Verify Results**: Check file changes and run tests
7. **Continue Work**: Resume in Antigravity with updated codebase

### Agent Selection Guide

```
Is it a complex multi-step task?
├── Yes → Use Sisyphus (default) or ultrawork
└── No
    ├── Frontend/UI work? → Use Frontend agent
    ├── Architecture/debugging? → Use Oracle
    ├── Need docs/research? → Use Librarian
    └── Quick code search? → Use Explore
```

### When to Bring Work Back

Return work to Antigravity when:
- Browser testing is needed
- User interaction is required
- Results need integration with current conversation
- Visual verification is needed
</antigravity_integration>

---

## 8. Troubleshooting

<troubleshooting>
### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| `ProviderModelNotFoundError` | Invalid model identifier | Run `opencode models` to list available models |
| Agent not found | OMO not properly installed | Check `~/.config/opencode/oh-my-opencode.json` exists |
| Context too short | Auto-compact triggered | Task is self-contained; continue monitoring |
| Timeout | Long-running task | Increase `WaitMsBeforeAsync` or send to background |

### Agent-Specific Notes

- **Sisyphus**: Best for orchestration; will delegate to other agents automatically
- **Oracle**: Call when stuck in loops or need strategic direction
- **Frontend**: Optimized for Gemini 3 Pro's visual understanding
- **Librarian**: Excellent at digesting framework documentation
- **Explore**: Fastest for code search; less reasoning capability

### Debugging Commands
```bash
# Check OMO configuration
cat ~/.config/opencode/oh-my-opencode.json

# List available models  
opencode models

# List configured agents
opencode agent list
```
</troubleshooting>

---

## Quick Reference

```bash
# Ultrawork mode (recommended)
opencode run "ulw: Your task description"

# With specific agent
opencode run -a oracle "Architecture question here"
opencode run -a frontend "UI component task"
opencode run -a librarian "Research framework docs"
opencode run -a explore "Find pattern in codebase"
opencode run -a sisyphus "Complex multi-step task"
opencode run -a prometheus "Plan a complex task"
opencode run -a metis "Review a plan"
opencode run -a multimodal-looker "Analyze image"

# Check available agents
opencode agent list

# Check available models
opencode models
```