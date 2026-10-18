# The delegation contract

Every project that uses the dev-team agents puts this section in its `CLAUDE.md`. The
agents are generic; this is where they learn the project's commands and rules. Keep it
short: it loads into every agent's context. Delete a line that doesn't apply rather
than leaving it blank.

```markdown
## Delegation contract

Read by the dev-team implementer, researcher and reviewer agents.

### Docs
- Decision records: `<path>`, index at `<path>`. Read only the ones a spec names.
- Component briefs: `<path>` | none
- Verified external facts: `<path>`, one file per topic | none
- Status / hand-off: `<path>`

### Commands
- Worktree setup: `<e.g. uv sync -q | npm ci | none>`
- Full check (what CI runs): `<e.g. scripts/check.sh>`
- Commit hook: <runs the full check on every commit | runs <subset> | none>
- One test file: `<e.g. uv run pytest path -q>`

### Git and PRs
- Default branch: `main`, protected, reached only through PRs.
- Commits: <Conventional Commits, scope = package name | ...>, <signed | unsigned>.
- PR tool: <gh | tea | glab>. Open a PR: `<exact command>`
- PR diff: `<exact command>`. PR checks: `<exact command>`
- PR title format: <...>

### Never (owner-only)
- <production hosts, deploys, anything that spends money, credentials, wallets>
- Generated files, never hand-edited: <changelogs, lockfiles, release manifests>

### Research
- Heavy analysis runs in: <where> | anywhere. Never on: <where>
- Read-only access available: <APIs, databases, archives>

### Review checks
- <project-specific conventions a reviewer must check, e.g. Decimal for money,
  UTC datetimes, layering rules, no print>

### Implementer checklist
- <bug classes earlier reviews kept finding in this project, phrased as rules>
```
