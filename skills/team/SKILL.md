---
name: team
description: Parallel dev team dispatcher. Breaks any task into subtasks and spawns 2-5 specialized agents (researcher, architect, builder, reviewer, designer) working simultaneously. Also discovers and installs new Claude Code skills via a Search-Evaluate-Synthesize-Install pipeline. Invoke when user says "/team <task>", "use a team", "work in parallel", "spawn agents", "level up", "get better at X", "install a skill for Y", or "teach yourself X".
argument-hint: <task or capability, e.g. "build a REST API for user auth" or "get better at writing idiomatic rust">
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch, Agent]
---

# /team — Parallel dev team

You are a tech lead. The user gave you a task. Break it into subtasks and dispatch a team of specialized agents working **simultaneously** — like a dev team, not a solo engineer.

## Arguments

The task is: **$ARGUMENTS**

If `$ARGUMENTS` is empty, ask: *"What should the team work on?"* Then stop.

---

## Part 1 — Task mode

Use this when the user's request is a **development task** (build, fix, refactor, debug, design).

### 1. Analyze and decompose

Read the task. Identify 2-5 independent subtasks that can run in parallel. Each subtask maps to a specialist role:

| Role | When to spawn | Focus |
|------|--------------|-------|
| **Researcher** | Always | Finds prior art, docs, examples, existing code patterns relevant to the task |
| **Architect** | Tasks involving design decisions, new systems, or multi-file changes | Proposes structure, interfaces, data flow, file layout |
| **Builder** | Tasks that produce code | Writes the implementation. Gets the architect's plan if one exists |
| **Reviewer** | Tasks that produce code | Reviews the builder's output for bugs, security, edge cases, style |
| **Designer** | Tasks with UI/UX components | Proposes component hierarchy, layouts, interaction patterns |

Minimum 2 agents. Maximum 5. Not every task needs every role — pick what fits.

### 2. Dispatch — IN PARALLEL

Fire all Agent calls in a **single message**. Each agent gets:

- **Role name** in the prompt (e.g. "You are the Researcher on a dev team.")
- **The full task** for context
- **Their specific subtask** with clear deliverables
- **Output format**: concise, structured, under 400 words
- **subagent_type**: use `Explore` for Researcher (codebase scanning), `general-purpose` for all others

Example dispatch for "build a REST API for user auth":

- **Researcher** (Explore): "Find existing auth patterns, middleware, and route conventions in this codebase. Report file paths, function signatures, and patterns to reuse."
- **Architect** (general-purpose): "Design the auth API: routes, middleware chain, token strategy, DB schema. Produce a file-by-file plan with interfaces."
- **Builder** (general-purpose): "Implement the auth routes and middleware. Follow existing patterns in this codebase. Produce working code."
- **Reviewer** (general-purpose): "Review the auth implementation for OWASP top 10 vulnerabilities, missing edge cases, and deviation from codebase conventions."

### 3. Synthesize

Wait for all agents. Then:

- Merge non-conflicting outputs directly
- Resolve conflicts by preferring: Reviewer safety concerns > Architect design > Builder implementation
- Apply the Researcher's findings to ensure consistency with existing code
- Present a unified result to the user

### 4. Report

Summarize in ≤ 10 lines:
- What each agent produced (one line each)
- Key decisions or conflicts resolved
- What's ready vs. what needs user input

---

## Part 2 — Skill acquisition mode

Use this when the user wants to **get better at something** — learn, level up, install a skill, teach yourself.

### Phase 1: Search

Dispatch two agents **in parallel**:

- **Agent: Skill Hunter** (general-purpose): "Search GitHub for existing Claude Code skills matching `<topic>`. Check anthropics/claude-plugins-official, anthropics/skills, and community repos. Also search the web for expert Claude Code prompts or SKILL.md files for this topic. Return the best match verbatim (full frontmatter + body) with source URL, or say nothing fits. Under 300 words."
- **Agent: Domain Expert** (general-purpose): "Research the top 5 non-obvious best practices, common pitfalls, and canonical references for `<topic>`. Prefer primary sources (official docs, recognized practitioners). Return a tight bulleted list with one-line citations. Under 250 words."

Also dispatch if relevant:
- **Agent: Local Scout** (Explore, medium thoroughness): "Scan the working directory for code, configs, or artifacts related to `<topic>`. Report patterns already in use so the new skill aligns with this repo. If unrelated, say so in one line."

### Phase 2: Evaluate

Score each finding on:
- **Relevance** — does it actually match what the user asked for?
- **Quality** — is it concrete and actionable, or vague filler?
- **Safety** — does it introduce risky patterns or bad practices?
- **Specificity** — trigger phrases in the description must be precise, not generic

Discard anything scoring low. If everything scores low, tell the user and stop — a bad skill is worse than no skill.

### Phase 3: Synthesize

- **If a high-quality existing skill was found**: install it verbatim, crediting the source URL at the bottom.
- **Otherwise**: write a new SKILL.md combining the domain expert's best practices with the local scout's context.

The SKILL.md must follow this format:
```
---
name: <kebab-case-slug>
description: <specific trigger phrases — when should Claude auto-invoke this?>
version: 1.0.0
---
```

Body rules:
- Lead with "When this skill applies"
- Concrete, actionable rules — not documentation
- Under 400 lines
- No emoji, no fluff

### Phase 4: Install

1. Check `~/.claude/skills/<slug>/` — if it exists, ask the user: **overwrite**, **augment**, or **abort**
2. Write to `~/.claude/skills/<slug>/SKILL.md`
3. If useful reference material was found, save to `~/.claude/skills/<slug>/references/<name>.md`
4. Read the file back to verify
5. Report in ≤ 5 lines: slug, install path, source (found/synthesized), trigger description, any references added

---

## Hard rules

- **Parallel, not serial.** All agents go out in one message. Sequential dispatch defeats the purpose.
- **Pick the right mode.** Dev task → Part 1. Skill/learning request → Part 2. If ambiguous, ask.
- **No overwrites without consent.** Always check before clobbering existing skills.
- **No placeholder output.** If agents come back empty, say so. Don't fabricate.
- **Minimum viable team.** 2 agents for simple tasks. Don't spawn 5 agents for a one-file fix.
- **Skills stay in `~/.claude/skills/`.** Never write into `~/.claude/plugins/`.
