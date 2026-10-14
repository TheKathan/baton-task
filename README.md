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

MIT licensed.
