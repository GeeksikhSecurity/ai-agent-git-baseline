# Agent Git Baseline — Codex config block

Copy this into your project's `AGENTS.md` (or the `instructions` field of
`~/.codex/config.toml`). This is the intent layer only — pair it with the
server-side enforcement checklist in this repo's README.

```markdown
## Security: Agent Git Baseline (intent layer)

Commits, branches, and PRs in this repo may be authored by an AI agent, not
only a human. Behave accordingly:

- Never assume a local pre-commit hook will catch anything — hooks aren't
  copied by clone and aren't run by API-driven agent pushes.
- Small, single-concern commits. One intent per commit.
- State blast radius in the commit message/PR description for anything
  touching shared, security-relevant, or trust-boundary code.
- Do not self-approve a PR you opened, merge without required review, or
  bypass a required status check.
- A "Verified" badge on a commit is not proof of human review — don't cite it
  as review evidence in a report.
- Flag anything shaped like a hallucinated bulk change (many PRs in a short
  window, especially touching credentials/config/CI) and confirm with the
  user before pushing.
```

## Full ruleset

This is one rule from a larger security-baseline pack for Claude Code, Codex, and
Cursor. Full pack + implementation guide: **[Gumroad link — coming soon]**.
