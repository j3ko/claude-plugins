---
name: orchestrate
description: How the main session splits work with the dev-team implementer, researcher and reviewer subagents. Load before delegating any implementation, research or review, before writing a hand-off spec, and before resuming or relaunching a subagent.
---

# Orchestrating the dev team

The main session (the most capable model) keeps design, decisions and the discussion
with the owner, and keeps its own context free of implementation detail. Three
subagents do the rest:

| Agent | Model | Does | Never |
|---|---|---|---|
| `dev-team:implementer` | Sonnet, own worktree | Implements one spec, opens one PR | merges, widens scope |
| `dev-team:researcher` | Sonnet, read-only | Verifies facts about external systems, data and docs | edits, sends, signs |
| `dev-team:reviewer` | Opus, own worktree | Reviews one PR against its spec | fixes, pushes, approves |

The project's `CLAUDE.md` must carry a **Delegation contract** section, which every
agent reads. If it's missing, write one from [project-contract.md](project-contract.md)
with the owner before delegating anything.

## What to delegate, and what to do yourself

Delegate work that would fill your context with detail:

- **Implementation** → `implementer`. For a mechanical job, where the spec leaves
  nothing to judge (moving or renaming files, docs edits, applying changes spelled out
  line by line), override it to `model: haiku`. Anything that writes or changes logic
  stays on Sonnet.
- **Research** into APIs, docs, data and on-chain or archived records → `researcher`.
- **Broad codebase searches** → the built-in Explore agent.
- **Review** → `reviewer`, for every PR that changes code, scripts or CI, before it goes
  to the owner. Docs-only and config-only PRs skip it.

Do small or mechanical changes yourself. A subagent starts cold, so a change of a few
edits costs several times more delegated than done in place.

Agents run in the background. Run several implementers in parallel only when their
files don't overlap.

## Keeping the cost down

- **One concern per spec.** A spec that one implementer can finish in one run, and a
  reviewer can check in one pass, is cheaper than one large spec, even at three or four
  PRs instead of one.
- **Ask the owner first about anything they'll see or type** (config shapes, command
  names, alert or log wording) before the spec goes out, not after the code is done.
- **A resumed agent re-sends its whole transcript on every turn.** Before resuming one,
  check its worktree (`git status`, `git log`). If only a few edits are left, make them
  yourself. If more is left and its context is large, start a fresh agent with a short
  spec of only what remains.
- **A user-stopped agent can't be resumed.** Relaunch on a new branch; leave the old
  worktree for the owner to inspect.
- **Start a new session at natural boundaries**, such as after a PR lands, with a
  hand-off written where the project keeps it (a status file, or open issues in its
  tracker: the delegation contract says which), rather than carrying one session's
  context across several PRs. With issues, the hand-off is the issues the session
  touched: edit a body when its plan changed, comment with progress and findings, and
  open a new issue for work that's next.

## Writing a hand-off spec

Use [spec-template.md](spec-template.md). The spec names exact files, with line
numbers or `grep` patterns for the places to change, so the implementer doesn't
explore: most of an implementer's cost is finding its way around. A durable decision
goes into the project's decision records first, not only into the spec. The implementer
copies the spec into the PR body.

## Before the first delegation in a session

The implementer commits, pushes and opens a PR, and the reviewer runs the full check.
If the session can't grant those (a headless run, a restrictive permission mode), the
implementer stops at `blocked` with a finished but uncommitted worktree. Check that the
session allows them, or that the owner will be there to approve them, before launching
an implementer. The plugin's README lists the permission rules.

## The review loop

1. The implementer opens the PR and reports. If it reports `blocked` instead, stop the
   loop: relay the block to the owner with the worktree's path and branch, and don't
   do the refused step yourself, and don't review the uncommitted worktree instead.
   Leave the worktree for the owner. Take the branch name from the implementer's
   report, not from the worktree metadata: the harness names the worktree's starting
   branch, and the implementer switches to the spec's branch from there.
2. Check CI on the PR yourself; the implementer doesn't wait for it.
3. Launch the reviewer with the PR number and the spec, inline or by path. It can
   read the spec from the PR body, but not if the PR tool is out of reach.
4. Send the findings back to an implementer as a fix-up spec naming the PR's branch: a
   fresh agent with only the findings, unless the original's context is small. Don't
   fix them yourself unless they're a few edits.
5. Re-review only if the fix-up changed logic.
6. Hand the PR to the owner, relaying only what they need: what changed, anything the
   review found that they should know, and anything only they can do.

## Agent reports

Every agent ends with the same fixed format (Result, Summary, Deviations from spec,
Open questions, For the owner), and the reviewer adds a Findings block. The owner doesn't see agent reports: relay what
matters, and act on deviations and open questions before moving on.

## Working with the owner

- Open PRs; don't merge them unless asked explicitly.
- Keep to the scope that was asked for. List anything worth adding as a proposal and
  let the owner decide. If the project tracks work in issues, also open the proposal as
  an issue, so it outlives the session.
- Repo and server settings, secrets and anything outward-facing are the owner's call.
  Ask first.
- For a large task the owner wants to watch and steer live, suggest a separate session
  instead of a subagent.
