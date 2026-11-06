# claude-plugins

> **Archived.** This repo is replaced by [j3ko/agent-plugins](https://github.com/j3ko/agent-plugins),
> which carries the same dev-team workflow for both Claude Code and Codex. Switch with:
>
> ```sh
> claude plugin marketplace add j3ko/agent-plugins
> claude plugin install dev-team@j3ko-agents --scope project
> ```
>
> and disable the old `dev-team@j3ko`. Nothing here is updated any more.

Personal Claude Code plugins, as one marketplace named `j3ko`.

```sh
claude plugin marketplace add j3ko/claude-plugins
claude plugin install dev-team@j3ko
```

| Plugin | What it is |
|---|---|
| [dev-team](plugins/dev-team/) | An Opus main session that designs and delegates to implementer, researcher and reviewer subagents |

Plugins carry no `version`, so every commit to `main` is an update:
`claude plugin update dev-team@j3ko` picks it up.
