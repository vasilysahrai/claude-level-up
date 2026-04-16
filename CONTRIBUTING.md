# Contributing

Pull requests welcome. Here's what helps most:

- **Prompt improvements** to `skills/team/SKILL.md` — better agent prompts, smarter role selection, synthesis logic.
- **New agent roles** — if you've found a specialist pattern that works well (e.g. a "DevOps" agent for infra tasks), propose it.
- **Bug reports** — if `/team` produces bad output or spawns the wrong agents, file an issue with the task you tried and what went wrong.

## Testing locally

1. Copy `skills/team/SKILL.md` to `~/.claude/skills/team/SKILL.md`
2. Restart Claude Code (or `/reload-plugins`)
3. Run `/team <some task>` and verify it dispatches the right agents

## Style

Keep the SKILL.md focused on model-invoked guidance. No emoji, no fluff. The skill should read like instructions to a tech lead, not documentation for a user.
