<p align="center"><a href="https://github.com/TheKathan/baton"><picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/TheKathan/baton/v1/assets/banner-dark.svg"><img alt="baton: hand-offs for AI agents that build software together" src="https://raw.githubusercontent.com/TheKathan/baton/v1/assets/banner-light.svg" width="720"></picture></a></p>

<p align="center">
  <img alt="latest release" src="https://img.shields.io/github/v/release/TheKathan/baton-task">
  <img alt="license" src="https://img.shields.io/github/license/TheKathan/baton-task">
  <a href="https://github.com/TheKathan/baton"><img alt="baton 1.4+" src="https://img.shields.io/badge/baton-%E2%89%A5%201.4-F76707"></a>
  <img alt="runners: Linux, macOS" src="https://img.shields.io/badge/runners-Linux%20%7C%20macOS-555">
</p>

# baton task board

Gives an AI agent's task its own [baton](https://github.com/TheKathan/baton) board, then exports it for the pull request. Reviewers see the agents' questions, contracts, decisions and hand-offs next to the code, and the step can fail while a question is still open.

Put one step before the agent and one after it:

```yaml
- uses: actions/checkout@v4
- uses: TheKathan/baton-task@v1
  with: { mode: start, task: LIN-123 }

- run: ./run-my-agent.sh          # any agent; it uses the `baton` command

- uses: TheKathan/baton-task@v1
  id: board
  with: { mode: finish, task: LIN-123, strict: true }
# .baton/tasks/LIN-123.jsonl is now staged; commit and open the PR your way.
# The PR summary is in the job summary and at ${{ steps.board.outputs.summary-file }}.
```

`start` creates the task's board, or resumes it when the branch already has `.baton/tasks/<task>.jsonl`. It also takes the project's policies and roles from `.baton/config.json`. `finish` exports the board, stages it, and writes the summary.

| Input | Default | Meaning |
|---|---|---|
| `mode` | required | `start` (before the agent) or `finish` (after it). |
| `task` | required | The issue id, e.g. `LIN-123`. |
| `roles` | project roles | With `start`: comma-separated roles. |
| `strict` | `false` | With `finish`: fail while a question or blocker is open. |
| `stage` | `true` | With `finish`: `git add` the exported board. |
| `install` | `true` | Install baton with `TheKathan/setup-baton`. Set to `false` if an earlier step did. |
| `version` | `v1` | baton version to install. |

| Output | Meaning |
|---|---|
| `summary-file` | With `finish`: path of the Markdown PR summary. |
| `open` | With `finish`: number of open questions and blockers. |

Needs baton 1.4 or newer, on Linux or macOS runners. See [sandbox mode](https://github.com/TheKathan/baton/blob/main/docs/sandbox.md).

## Versions

Releases follow the same trunk-based semver as [baton](https://github.com/TheKathan/baton): every merged PR is released, a `feature/*` branch bumps the minor version and any other branch the patch version. Use `@v1` for the latest 1.x, or pin an exact tag such as `@v1.0.1`.

MIT licensed.
