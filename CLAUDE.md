# Agent Git Baseline — CLAUDE.md snippet

`CLAUDE.md` is the **intent layer** of this rule — it tells an agent what you
meant. It is advisory only: an agent (yours or someone else's, working against a
fork/clone of this repo) can ignore it, and a `CLAUDE.md` file is never copied by
`git clone` in a way that constrains anything server-side. Treat this file as
step 1 of 3, not the whole control. See [README.md](./README.md) for the
enforcement layer (server-side, non-bypassable) and the attribution layer this
rule assumes exist alongside it.

## Full block

```markdown
## Rule: Agent Git Baseline (intent layer — pair with server-side enforcement)

This repo assumes commits, branches, and PRs may be authored by an AI agent
(this assistant or another one), not only a human at a keyboard. Behave
accordingly:

1. **Never assume a local pre-commit hook will catch anything.** Hooks live in
   `.git/hooks`, are not copied by `git clone`, and are not run by API-driven
   agents pushing via a platform's GraphQL/REST mutation path. If a check
   matters, it must be a server-side branch ruleset or required status check —
   not a hook you're relying on silently.
2. **Small, single-concern commits.** One intent per commit. This is both a
   Tidy-First practice and a security one: a commit that mixes an unrelated
   dependency bump with a feature change is harder for a reviewer (human or
   agent) to evaluate for blast radius.
3. **State blast radius in the commit message or PR description** for anything
   touching shared, security-relevant, or trust-boundary code: what this
   changes, what could break, who/what else depends on it.
4. **Do not self-approve.** If this agent opened the PR, it does not also mark
   it approved, merge without required review, or bypass a required status
   check — even if it has the technical permission to do so via an API token.
5. **Assume "Verified" on a commit is not proof of human authorship.**
   Server-created commits (via API/GraphQL) can be marked "Verified" by the
   platform without a human having reviewed the diff. Don't cite the checkmark
   as evidence of review in a report or audit trail — cite the actual review
   record.
6. **Flag anything that looks like a hallucinated bulk change.** Opening many
   PRs in a short window, especially ones touching credentials, config, or CI
   definitions, is a known agent failure mode (documented case: 40 PRs
   overnight containing plaintext credentials from a single malformed prompt).
   Stop and confirm with the user before a bulk push of this shape.
```

## Compact block

```markdown
## Rule: Agent Git Baseline
Small, single-concern commits; state blast radius for shared/security code;
never self-approve or bypass required review; don't cite a "Verified" badge as
proof of human review — that badge survives server-side agent commits too.
Local git hooks do not constrain API-driven agent pushes — real enforcement is
server-side (see README: branch rulesets, push protection, signed commits).
```

## Full ruleset

This is one rule from a larger security-baseline pack for Claude Code, Codex, and
Cursor. Full pack + implementation guide: **[Gumroad link — coming soon]**.
