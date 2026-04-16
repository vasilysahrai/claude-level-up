# claude-level-up

Teach Claude Code new tricks. It learns them itself.

Tell Claude what to get better at. It dispatches a team of parallel research agents, synthesizes what they find into a new skill, and installs it — so every future session benefits.

## How it works

```
You: /level-up reviewing SQL migrations for safety

Claude dispatches 3 agents in parallel:
  +-----------------------+  +-------------------------+  +---------------------+
  | Agent A               |  | Agent B                 |  | Agent C             |
  | Hunts GitHub for      |  | Researches best         |  | Scans your local    |
  | existing skills       |  | practices & pitfalls    |  | codebase for context|
  +-----------------------+  +-------------------------+  +---------------------+
              \                        |                        /
               \                       |                       /
                +----------------------+-----------------------+
                |  Synthesize or install the best match        |
                +----------------------------------------------+
                                       |
                                       v
              ~/.claude/skills/sql-migration-review/SKILL.md
```

The installed skill auto-activates in future sessions whenever Claude detects a matching context.

## Install

### Option A: Clone and copy (recommended)

```bash
git clone https://github.com/vasilysahrai/claude-level-up.git
cp -r claude-level-up/skills/level-up ~/.claude/skills/level-up
```

Then restart Claude Code or run `/reload-plugins`.

### Option B: One-liner

```bash
mkdir -p ~/.claude/skills && git clone https://github.com/vasilysahrai/claude-level-up.git /tmp/claude-level-up && cp -r /tmp/claude-level-up/skills/level-up ~/.claude/skills/level-up && rm -rf /tmp/claude-level-up
```

## Usage

```
/level-up writing idiomatic rust
/level-up reviewing SQL migrations for safety
/level-up "tailwind v4 conventions"
/level-up debugging memory leaks in Node.js
```

Each invocation:
1. Normalizes your request into a kebab-case slug
2. Checks for existing skills to avoid duplicates
3. Dispatches three research agents **in parallel**
4. Installs the result to `~/.claude/skills/<slug>/SKILL.md`

If an existing high-quality skill is found on GitHub, it installs that verbatim (with credit). Otherwise it synthesizes a new one from best practices and your local codebase context.

## What gets installed

Each skill is a single `SKILL.md` file with:
- **Frontmatter**: name, description (trigger phrases for auto-activation), version
- **Body**: concrete, actionable guidance — not documentation, but instructions Claude follows when the skill activates
- **References** (optional): cheat sheets or canonical examples in a `references/` subdirectory

Skills live in `~/.claude/skills/<slug>/` and persist across sessions.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- The `Agent`, `WebSearch`, and `WebFetch` tools must be available (they are by default)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE)
