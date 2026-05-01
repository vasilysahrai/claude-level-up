# claude-level-up

A dev team for Claude Code, not a solo engineer.

`/team` breaks your task into subtasks and spawns 2-5 specialized agents in parallel — researcher, architect, builder, reviewer, designer — each working simultaneously. It also discovers and installs new skills so Claude gets permanently smarter.

## Two modes

### Task mode — parallel dev team

```
You: /team build a REST API for user auth

Claude spawns a team:
  +------------+  +------------+  +-----------+  +------------+
  | Researcher |  | Architect  |  | Builder   |  | Reviewer   |
  | Finds      |  | Designs    |  | Writes    |  | Checks for |
  | existing   |  | routes,    |  | the code  |  | bugs,      |
  | patterns   |  | schema,    |  |           |  | security,  |
  | & prior art|  | interfaces |  |           |  | edge cases |
  +------------+  +------------+  +-----------+  +------------+
        \               \              /               /
         +---------------+-----------+----------------+
         |         Synthesize unified result           |
         +---------------------------------------------+
```

Each agent works in parallel. Results merge with a priority order: reviewer safety concerns > architect design > builder implementation, grounded by researcher findings.

### Skill mode — Search, Evaluate, Synthesize, Install

```
You: /team get better at writing idiomatic rust

  Phase 1: SEARCH
  +------------------+  +------------------+  +----------------+
  | Skill Hunter     |  | Domain Expert    |  | Local Scout    |
  | GitHub, community|  | Best practices,  |  | Your codebase  |
  | repos, web       |  | pitfalls, docs   |  | patterns       |
  +------------------+  +------------------+  +----------------+

  Phase 2: EVALUATE
  Filter for relevance, quality, safety, specificity

  Phase 3: SYNTHESIZE
  Combine into a single focused SKILL.md

  Phase 4: INSTALL
  ~/.claude/skills/idiomatic-rust/SKILL.md
```

The installed skill auto-activates in every future session.

## Install

### Option A: Clone and copy (recommended)

```bash
git clone https://github.com/vasilysahrai/claude-level-up.git
cp -r claude-level-up/skills/team ~/.claude/skills/team
```

Then restart Claude Code or run `/reload-plugins`.

### Option B: One-liner

```bash
mkdir -p ~/.claude/skills && git clone https://github.com/vasilysahrai/claude-level-up.git /tmp/claude-level-up && cp -r /tmp/claude-level-up/skills/team ~/.claude/skills/team && rm -rf /tmp/claude-level-up
```

## Usage

### Dev tasks

```
/team build a REST API for user auth
/team refactor the payment module to use Stripe v3
/team debug why websocket connections drop after 30s
/team design a caching layer for the search endpoint
```

### Skill acquisition

```
/team get better at writing idiomatic rust
/team teach yourself SQL migration safety
/team install a skill for tailwind v4 conventions
/team level up at debugging memory leaks in Node.js
```

## Agent roles

| Role | When spawned | Focus |
|------|-------------|-------|
| **Researcher** | Always | Prior art, docs, existing patterns in your codebase |
| **Architect** | Design decisions, new systems, multi-file changes | Structure, interfaces, data flow, file layout |
| **Builder** | Tasks that produce code | Implementation, following existing patterns |
| **Reviewer** | Tasks that produce code | Bugs, security (OWASP), edge cases, style |
| **Designer** | UI/UX tasks | Component hierarchy, layouts, interactions |

Minimum 2 agents. Maximum 5. The team scales to fit the task.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- The `Agent`, `WebSearch`, and `WebFetch` tools must be available (they are by default)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE)

## Made by vasilysahrai, on GitHub
