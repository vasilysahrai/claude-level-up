# Contributing

Pull requests welcome. Here's what helps most:

- **Prompt improvements** to `skills/level-up/SKILL.md` — better agent prompts, smarter synthesis logic, edge-case handling.
- **Reference templates** — if you've written a great skill for a specific domain (Rust, SQL, React, etc.), open a PR adding it as an example in the README.
- **Bug reports** — if `/level-up` produces a broken or useless skill, file an issue with the topic you tried and what went wrong.

## Testing locally

1. Copy `skills/level-up/SKILL.md` to `~/.claude/skills/level-up/SKILL.md`
2. Restart Claude Code (or `/reload-plugins`)
3. Run `/level-up <some topic>` and verify it produces a working skill

## Style

Keep the SKILL.md focused on model-invoked guidance. No emoji, no fluff, no multi-paragraph docstrings. The skill should read like instructions to a colleague, not documentation for a user.
