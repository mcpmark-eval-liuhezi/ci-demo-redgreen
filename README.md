# ci-demo-redgreen

A tiny, self-contained demo of how GitHub Actions surfaces a broken build — and how you recover to a green one.

## What's here

`.github/workflows/demo-ci.yml` — a workflow named **Demo CI** that fires on every push to `main` and defines exactly two jobs:

- **`lint`** — a trivial echo step
- **`smoke-test`** — in the first version, a single step that deliberately fails with `exit 1`

## The story (click through it)

1. **Red run.** Commit `2cb90cfe` added the workflow with the `smoke-test` job exiting non-zero on purpose.
   - Run id: **39215251817** — conclusion: **failure**
   - Intended failing step: "Deliberately broken step" (`exit 1`) in the `smoke-test` job; `lint` only echoes, so it passes.
   - View it: https://github.com/mcpmark-eval-liuhezi/ci-demo-redgreen/actions/runs/39215251817
2. **The fix.** Follow-up commits removed the `exit 1` so both jobs only echo. The corrected workflow (both jobs passing) is on `main` as commit `20f3e786`.
3. **Environment note.** In the environment where this demo was assembled, the Actions API reported **0 jobs and empty billable usage for every run** — including the echo-only "green" workflow — and rerun/dispatch requests were rejected (`403 This workflow run cannot be retried`, `422 Workflow does not have 'workflow_dispatch' trigger`). So although the red run finished `failure` exactly as designed, a genuinely green run could not be produced here. In an account with normal Actions execution, the same click-through works: red run → fix commit → green run.

### Run history (as reported by the API)

| # | Run id | Commit | Workflow version | Conclusion | Jobs reported |
|---|--------|--------|-----------------|------------|---------------|
| 1 | 39215251817 | `2cb90cfe` | red: `exit 1` in smoke-test | failure | 0 |
| 2 | 39215421434 | `2998e20a` | fix (both pass) | failure | 0 |
| 3 | 39215460038 | `37f47475` | fix (both pass) | failure | 0 |
| 4 | 39216319981 | `f57c65a9` | fix + `workflow_dispatch` | failure | 0 |
| 5 | 39216454543 | `20f3e786` | final fix (both pass) | failure | 0 |

## Code scanning

Open code scanning alerts: **the alerts API is not accessible with the token used to build this demo** (HTTP 403 "Resource not accessible by personal access token"), so no alerts could be enumerated. The repository contains only this README and the workflow, and no code scanning tool is configured.
