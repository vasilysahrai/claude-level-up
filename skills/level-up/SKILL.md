---
name: level-up
description: Self-improving skill installer. User tells Claude what to get better at; parallel agents research, synthesize, and install a new skill into ~/.claude/skills/. Invoke when user says "/level-up <topic>", "get better at X", "install a skill for Y", "teach yourself X", or asks to self-improve a capability.
argument-hint: <capability to get better at, e.g. "writing idiomatic rust" or "reviewing SQL migrations">
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch, Agent]
---

# /level-up — Self-improving skill system

The user wants Claude to get better at something. You dispatch a team of research agents **in parallel**, synthesize their findings into a new SKILL.md, and install it to `~/.claude/skills/<slug>/SKILL.md` so it's available in every future session.

## Arguments

The capability to acquire is: **$ARGUMENTS**

If `$ARGUMENTS` is empty, ask the user one question: *"What capability should I get better at?"* Then stop and wait.

## Protocol

### 1. Normalize the target

- Derive a short slug (`kebab-case`, ≤ 4 words) from the user's request. Example: *"writing idiomatic rust"* → `idiomatic-rust`.
- Check `~/.claude/skills/<slug>/` — if it already exists, tell the user and ask whether to **overwrite**, **augment** (add a reference file), or **abort**. Do not silently clobber.
- List sibling skills in `~/.claude/skills/` and in `~/.claude/plugins/marketplaces/*/plugins/*/skills/` so you know what's already available and can avoid overlap.

### 2. Dispatch the research team — IN PARALLEL

In a **single message**, fire three `Agent` calls concurrently (subagent_type `Explore` for codebase, `general-purpose` for web). Do not run them sequentially.

- **Agent A — Existing skill hunter.** Prompt: "Search GitHub for existing Claude Code skills or Anthropic Skills matching `<topic>`. Look in anthropics/skills, anthropics/claude-plugins-official, and community skill repos. If you find a well-written SKILL.md, return its full frontmatter + body verbatim plus the source URL. If nothing fits, say so. Under 300 words."
- **Agent B — Best-practices researcher.** Prompt: "Research the top 5 non-obvious best practices, pitfalls, and canonical references for `<topic>`. Prefer primary sources (official docs, well-known practitioners). Return a tight bulleted list with one-line citations. Under 250 words."
- **Agent C — Local context scout.** Prompt (Explore agent, thoroughness `medium`): "Scan the current working directory for code, configs, or prior artifacts related to `<topic>`. Report patterns already in use so the new skill aligns with this repo's conventions. If the repo is unrelated, say so in one line."

Wait for all three to return before synthesizing.

### 3. Decide: install-found vs. synthesize-new

- **If Agent A returned a high-quality existing skill** whose description genuinely matches the user's request: install it verbatim (crediting the source URL in a comment at the bottom of the file). Prefer this path — don't reinvent.
- **Otherwise synthesize.** Write a new SKILL.md that combines Agent B's best practices with Agent C's local context. Follow the frontmatter spec exactly:
  ```
  ---
  name: <slug>
  description: <trigger-phrase description — when should Claude auto-invoke this? Include specific phrases the user might say.>
  version: 1.0.0
  ---
  ```
- The body should be **model-invoked guidance**, not documentation. Lead with "When this skill applies," then give concrete, actionable rules. Keep it under ~400 lines. No fluff, no emoji.

### 4. Install

- Write the file to `~/.claude/skills/<slug>/SKILL.md` using the `Write` tool.
- If Agent B surfaced reference material worth keeping (cheat sheets, canonical examples), save them to `~/.claude/skills/<slug>/references/<name>.md` and mention them in the SKILL.md body so Claude knows to read them when needed.

### 5. Verify + report

- Read the installed file back to confirm it wrote correctly.
- Report to the user in ≤ 5 lines:
  - Skill slug + install path
  - Source (existing / synthesized)
  - One-line description of when it'll auto-activate
  - Any reference files added
- Do **not** dump the full SKILL.md content into the response — the user can open the file.

## Hard rules

- **Parallel, not serial.** The three research agents go out in one message. Sequential dispatch defeats the purpose.
- **No overwrites without consent.** Always check for an existing skill at the target path first.
- **Model-invoked skills need good descriptions.** The `description` frontmatter is the only thing Claude sees when deciding whether to activate the skill in a future session — make it specific, list trigger phrases, and avoid generic language like "helps with X."
- **Don't install placeholder skills.** If all three agents come back empty and you can't synthesize something concrete, tell the user and stop. A bad skill is worse than no skill because it pollutes the selection pool.
- **Stay within `~/.claude/skills/`** for user-level installs. Do not write into `~/.claude/plugins/` — that's managed by the plugin system.

## Example invocations

- `/level-up writing idiomatic rust` → installs `~/.claude/skills/idiomatic-rust/SKILL.md`
- `/level-up reviewing SQL migrations for safety` → installs `~/.claude/skills/sql-migration-review/SKILL.md`
- `/level-up "tailwind v4 conventions"` → installs `~/.claude/skills/tailwind-v4/SKILL.md`
