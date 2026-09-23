# ai-agent-git-baseline

[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/GeeksikhSecurity/ai-agent-git-baseline/badge)](https://securityscorecards.dev/viewer/?uri=github.com/GeeksikhSecurity/ai-agent-git-baseline) [![Security Policy](https://img.shields.io/badge/security-policy-blue)](https://github.com/GeeksikhSecurity/ai-agent-git-baseline/security/policy)

> Minimum security-baseline rule for Claude Code, Codex, and Cursor. This free
> rule closes a real gap in each tool's built-in review. Full ruleset +
> implementation guide: **[Gumroad link — coming soon]**.

## A `CLAUDE.md` file will not stop an agent from pushing a bad commit. Here's the rest of what will.

Claude Code, Codex, and Cursor all let you write a rules file that tells the
agent what you meant. None of that is enforcement — a rules file lives
client-side, and an AI agent pushing commits through a platform's API does not
run your local git hooks at all.

**The gap:** traditional client-side security controls (local pre-commit hooks)
live exclusively in your local `.git/hooks` directory. Cloning a repo does not
copy them, and API-driven agents don't run a local git process — they bypass
client-side validation entirely. A `CLAUDE.md` rule is *advisory*: an agent can
ignore it, and nothing stops the push.

**Documented failure shape:** an agent hallucinating from a malformed prompt has
opened 40 PRs overnight containing plaintext credentials — sailing past local
secret scanners seamlessly. Worse, when the platform creates those commits
server-side via API mutation, they can be marked **"Verified"** automatically,
manufacturing a false sense of provenance for something no human reviewed.

## Why this is three layers, not one file

An agent-authored-git policy that lives only in a `CLAUDE.md`/`.cursor/rules`/
`AGENTS.md` file is a dead policy the moment an agent (yours, or a contributor's)
decides not to follow it. This repo ships all three layers so the gap doesn't
reopen at layer 2:

### Layer 1 — Intent (this repo's rule files)
What you meant. See [`CLAUDE.md`](./CLAUDE.md),
[`.cursor/rules/agent-git-baseline.mdc`](./.cursor/rules/agent-git-baseline.mdc),
[`codex/AGENTS.md`](./codex/AGENTS.md). **Advisory. Assume it can be ignored.**

### Layer 2 — Enforcement (server-side, non-bypassable)
Configure these at the GitHub repo/org level — not in a file an agent can edit:

- **Branch rulesets + required status checks**, set at the repository server
  layer (Settings → Rules → Rulesets), never relying on a client-side hook.
- **Secret scanning push protection** — intercepts a secret in transit, before
  it lands in history:
  ```
  gh api -X PATCH repos/{owner}/{repo} \
    -f security_and_analysis[secret_scanning_push_protection][status]=enabled
  ```
- **Separation of duties** — author cannot approve their own PR; approval is
  invalidated on new commits; admins cannot bypass required review.
- **Signed commits required** (GPG / SSH / Sigstore Gitsign) — reject unsigned
  PRs at the ruleset level.
- **Agent identity via short-lived tokens** — replace long-lived human PATs with
  fine-grained, repo-scoped installation tokens (~1hr expiry) for anything
  pushing as an agent.

### Layer 3 — Attribution & volume governance
The layer most teams skip, and the one that fails first:

- Use **SLSA v1.2**'s distinction between a *"trusted person"* and a *"trusted
  robot"* in the Source Track — don't let agent commits hide inside
  human-authored provenance vocabulary.
- Tag every agent-authored merge in deploy metadata so incident response can
  query for a given agent's blast radius after the fact.
- Pre-filter agent-opened PRs with a review agent so a human's attention goes to
  architectural judgment, not diff-reading volume.
- Write down, explicitly, that AI-assisted code gets specialized security
  review. **"We trust the AI" is not a compliance policy.**

## The datapoint that motivates this

Early 2026: AI-generated bug bounty submissions reached ~20% of total volume on
at least one major program. The confirmed-vulnerability rate **collapsed from
>15% to <5%**. Human triage became impossible and the program was terminated.
Internally, the same dynamic nullifies branch protection: overwhelmed reviewers
rubber-stamp, and subtle hallucinations flow into `main`. When generation
capacity explodes, **judgment becomes the scarce resource** — which is exactly
what layer 3 is trying to protect.

## This repo contains

- [`CLAUDE.md`](./CLAUDE.md) — intent-layer rule, full + compact block
- [`.cursor/rules/agent-git-baseline.mdc`](./.cursor/rules/agent-git-baseline.mdc) — Cursor rule file
- [`codex/AGENTS.md`](./codex/AGENTS.md) — Codex CLI config block
- This README — the layer-2/layer-3 checklist that no editor rule file can
  enforce on its own

## Try it yourself

1. Drop the intent-layer rule for your tool into place.
2. Check whether your repo currently has *any* of the layer-2 controls above
   configured (`gh api repos/{owner}/{repo} --jq .security_and_analysis`, and
   check Settings → Rules → Rulesets).
3. If layer 2 is empty, the intent-layer rule is the only thing standing
   between an agent and a bad push — and it can be ignored by design.

## Board Talking Points

"We have a CLAUDE.md rule for this" is not a compliance answer if nothing
server-side enforces it — a client-side file is bypassed entirely by any
API-driven agent. The three-layer split gives you a concrete, verifiable answer
to "what stops an agent from pushing something bad" instead of a policy
document nobody's checking.

## Full ruleset

This is one rule from a larger security-baseline pack for Claude Code, Codex, and
Cursor, plus an implementation guide. One-time purchase, no subscription:
**[Gumroad link — coming soon]**.

---

*Part of a rotating series — one live gap, one rule, one "try it yourself" call
to action — from [SecurityLeader.ai](https://securityleader.ai).*

## Part of an A/B test

This rule is one of three free-tier candidates being tested in parallel, each
in its own repo, to see which one earns the most GitHub stars/forks/clones and
blog engagement before the full paid rules pack is built:

- [ai-secrets-echo-guard](https://github.com/GeeksikhSecurity/ai-secrets-echo-guard) — Candidate 1
- [ai-agent-git-baseline](https://github.com/GeeksikhSecurity/ai-agent-git-baseline) — Candidate 2
- [ai-tool-poisoning-guard](https://github.com/GeeksikhSecurity/ai-tool-poisoning-guard) — Candidate 3

