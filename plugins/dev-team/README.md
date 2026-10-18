# dev-team

A Claude Code plugin for one way of working: the main session (Opus) designs, decides
and talks to the owner, and delegates the detail to three subagents.

- **`dev-team:implementer`** (Sonnet, own worktree): implements one hand-off spec and
  opens one PR.
- **`dev-team:researcher`** (Sonnet, read-only): verifies facts about external systems
  and reports how each was checked.
- **`dev-team:reviewer`** (Opus, own worktree): reviews a PR against its spec before the
  owner sees it.
- **`/dev-team:orchestrate`** (skill): when to delegate and when not to, the spec
  template, the review loop, and the cost lessons.

## Install

```sh
claude plugin marketplace add <git URL of this repo>
claude plugin install dev-team@j3ko
```

To enable it for everyone working in a repo, add it to that repo's
`.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "j3ko": { "source": { "source": "git", "url": "<git URL of this repo>" } }
  },
  "enabledPlugins": { "dev-team@j3ko": true }
}
```

## What a project must provide

The agents are generic. Everything project-specific (the check command, the PR tool,
where decisions and facts live, what's owner-only, the project's review checks) goes in
a **Delegation contract** section of the project's `CLAUDE.md`, which every subagent
already has in its context. The template is
[skills/orchestrate/project-contract.md](skills/orchestrate/project-contract.md). Where
the contract and an agent's own instructions disagree, the contract wins.

## Permissions

The implementer runs `git commit`, `git push` and the project's PR command, and the
reviewer runs the project's full check. In an interactive session the owner approves
them as they come up. A headless session (`claude -p`) can't ask, so it refuses them
unless they're allowed in advance, for example in the project's `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Bash(git:*)",
      "Bash(<the contract's setup and check commands>)",
      "Bash(<the contract's PR tool, for reads and writes>:*)"
    ]
  }
}
```

The main session needs the PR tool's reads too (checks, the PR body), and so does the
reviewer. A `:*` rule matches a prefix up to a word boundary only:
`Bash(tea api repos/o/r:*)` doesn't match `tea api repos/o/r/pulls/1`, so allow the
tool itself (`Bash(tea api:*)`) or each path you need.

Allow rules don't beat a `PreToolUse` hook that returns `ask`: a hook that gates
`git commit` and `git push` still prompts under any permission mode. Such a hook
needs the repo on its own exception list before a headless run can commit.

Without these, the implementer stops at `blocked` with its work finished but
uncommitted in its worktree.

## Notes

- Plugin agents are namespaced, so `dev-team:implementer` and a project's own
  `.claude/agents/implementer.md` can both exist. Retire the project's copy once its
  specifics have moved into the contract, or the main session will see two implementers.
- Plugin agents can't declare `hooks`, `mcpServers` or `permissionMode`; those fields are
  ignored. Nothing here uses them.
- Changes to agent definitions load at session start: start a new session after
  updating the plugin.
- Run `claude plugin validate .` before tagging a release.
