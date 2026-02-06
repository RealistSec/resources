<div align="center">
  <img src="../shared/images/realistSecLogoTransparent2.png" alt="RealistSec Logo" width="200">

  # Using OpenCode as Extra Staff with Antigravity

  *A workflow for delegating tasks between AI agents*
</div>

---

Taken from my X reply: https://x.com/RealistSec/status/2015751559292817471

---

**Opencode** is primarily a **CLI tool**,  
**Antigravity** is an **IDE with GUI**,

**Opencode** is basically the open-source **Claude Code**, that can use any model and also use **Claude (Anthropic)** models to act like **Claude Code**.

However...

Because **Antigravity** can use the **CLI** and **Opencode** is a **CLI app**, you can ask **Antigravity** to **use** **Opencode** → this is the **unlock**.

Instead of **Antigravity** having one **orchestration agent** with **sub-agents**, per task,

It can now **delegate tasks** to **Opencode** to orchestrate too.

So you end up with a flow where:

> **User Task** → **Antigravity (Orchestrator)** → delegates some to **Antigravity**, some to **Opencode** → **Opencode** sends finished work to **Antigravity** → **Antigravity** bug tests, quality controls and refactors if needed → Completes task and hands back to the user.

It's just like adding another **staff member** to the loop who thinks slightly differently to the PM.

You essentially get **two sets of eyes** on a task using slightly different **thought processes** and have grown your team with minimal effort.

---

## Practical Setup

Practically to do this, set up **Opencode** on your device, then make sure to have a shared **agent `.md`** (tells both the rules for your project/repo) and ask **Antigravity** to:

```
"create a project agent.md, a workflow (/run_opencode), an Agent and a Skill so that you (Antigravity) can delegate tasks to Opencode"
```

Then either just use **Antigravity** as normal (allow it to **auto select** when you use it) or if you know you are about to embark on a **specialist task** or something complex use the new **skill** **Antigravity** created for you by issuing a **slash command**:

```
[(Prompt) /run_opencode ]
```
