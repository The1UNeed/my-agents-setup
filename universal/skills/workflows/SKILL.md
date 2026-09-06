---
name: workflows
description: Invoke this skill when the user asks for a workflow, says "ultracode", or when a task needs several subagents run in ordered phases
metadata:
  harness: [pi]
  platform: [darwin, linux]
  scope: fleet
---

# Workflows

The `workflow` tool runs a JavaScript orchestration script you write inline. The script runs ordered phases and fans work out to isolated subagents. Each agent chooses its own harness, so one run can mix pi, Claude Code, and Codex.

Use `workflow` when a task needs several agents with phase dependencies or dynamic fan-out. For one or two independent delegations, use `subagent_spawn`. Only call this tool when the user says "ultracode" or explicitly asks for a workflow.

# Pi Harness

**Harness:** `pi`
**Best default:** Use when the user does not request another harness. It inherits the parent model and thinking level when `model` or `reasoning_effort` is omitted. This should be the defualt harness to use.

Do not use models from the Anthropic provider even if one appears in the model list.

Pi can use any model shown by `pi --list-models`. Prefer `provider/model-id`; a bare model id only works when unambiguous. Common picks in this environment:

| Model                            | Recommended effort |
| -------------------------------- | ------------------ |
| inherited parent model (default) | inherited          |
| `openai-codex/gpt-5.6-sol`       | `high`             |
| `openai-codex/gpt-5.6-terra`     | `high`             |
| `kimi-coding/k3`                 | `max`              |

**Thinking budgets:** `off`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`. These map directly to pi thinking levels.

## Claude Code Harness

**Harness:** `claude`
**Best default:** use the latest claude models on high reasoning. Do not default to anything else, if the user does not specify, use opus.

| Model hint | Model               | Recommended effort |
| ---------- | ------------------- | ------------------ |
| `opus`     | latest Claude opus  | `high`             |
| `fable`    | latest Claude Fable | `high`             |

**Thinking budgets:** `off`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`. The extension maps these to Claude thinking-token budgets: 0, 1,024, 4,096, 10,000, 16,000, 32,000, and 63,999 tokens respectively.

Requires Claude Code to be installed and authenticated.

## Codex Harness

**Harness:** `codex`
**Best default:** `gpt-5.6-sol` with `high` effort for coding work. Do not use anything other than sol unless the user specifically asks for it. Only use codex when it is related with computer use otherwise working with OpenAI model defualt to the pi harness.

| Model           | Recommended effort |
| --------------- | ------------------ |
| `gpt-5.6-sol`   | `high`             |
| `gpt-5.6-terra` | `high`             |
| `gpt-5.6-luna`  | `high`             |

**Thinking budgets accepted by the extension:** `off`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`. Codex maps these to the nearest effort supported by the selected model; `off`/`minimal` become `minimal`, while `max` becomes the highest extension-supported Codex effort.

Requires the Codex CLI to be installed and authenticated.

## Usinng the tool

`agent()` takes the same `harness`, `model`, and `effort` as `subagent_spawn`, with the same valid values. **Use the [subagents skill](../subagents/SKILL.md) to choose a harness and model — it is the single source of truth.** In short: `claude` for judgment and synthesis (`opus`), `codex` for bulk mechanical work (`gpt-5.6-sol`), `pi` for everything else (`provider/model-id`, inherits the parent when omitted).

Two names differ from `subagent_spawn`, and both spellings work:

| `subagent_spawn`   | `agent()` | 
| ------------------ | --------- |
| `name`             | `label`   |
| `reasoning_effort` | `effort`  |

`agent()` adds `phase` (which phase this belongs to) and `schema` (JSON Schema for a structured result).

Any other option key fails that agent with an error naming it. Options are never silently ignored — if an agent reports an unknown option, fix the key rather than working around it.

## Writing the script

```js
export const meta = {
  name: 'reliability-review',
  description: 'Review modules, then report',
  phases: [{ title: 'Scan' }, { title: 'Report' }],
}

const FINDINGS = {
  type: 'object',
  properties: { issues: { type: 'array', items: { type: 'string' } } },
  required: ['issues'],
}

phase('Scan')
const scans = await parallel(
  args.files.map((f) => () =>
    agent(`Review ${f} for correctness risks.`, {
      label: `scan:${f}`, phase: 'Scan', schema: FINDINGS,
      harness: 'codex', model: 'gpt-5.6-sol', effort: 'high',
    })),
)
const findings = scans.filter((r) => r.ok).map((r) => r.structured)

phase('Report')
const report = await agent(`Summarize: ${JSON.stringify(findings)}`, {
  label: 'report', phase: 'Report', harness: 'claude', model: 'opus',
})

return { findings, report: report.ok ? report.output : report.error }
```

- `export const meta = { name, description, phases }` — declare every phase up front.
- `phase(title)` — mark runtime progress, using titles from `meta.phases`.
- `await agent(prompt, opts)` — one subagent, always awaited.
- `await parallel([() => agent(...)], { concurrency })` — takes zero-argument **thunks**, not promises. Passing promises throws. `concurrency` can only lower the cap of 4.
- `args` — the parsed `args` tool parameter.
- `return` a JSON-serializable aggregate.

The script runs in a restricted child: no imports, eval, timers, filesystem, network, or process APIs. Limits are 32 agent calls per run and 45s for an agent to show first activity; there is no overall deadline.

Transient agent failures (harness stalls, dropped connections, unparseable structured output) retry automatically inside the same `agent()` call — up to 5 attempts with backoff — before it resolves `ok: false`. Retries do not consume extra agent-call budget. Do not write your own retry loops for these; permanent errors (auth, quota, unknown model/options) fail immediately and retrying them in the script will not help.

## Three rules that break runs

**Check `.ok`.** `agent()` never throws. It resolves to `{ ok, output, structured?, error? }`. Reading `.output` without checking `.ok` silently propagates an empty string into later phases.

```js
const r = await agent(...)
if (!r.ok) return { failed: r.error }
```

**Every child is blind.** It cannot see this conversation, ask the user, or spawn its own agents. Give each one absolute paths, constraints, and the exact report you want back.

**Schema reliability differs by harness.** `claude` validates at the API level and `pi` validates a tool call; `codex` is asked for JSON and parsed, which can fail. Put schema-dependent steps on `claude` or `pi`.

## After the call

Runs block by default with live progress. Pass `background: true` for a run id now and a follow-up message on completion.

- `/workflows` lists runs; `/workflows <runId>` shows one run's detail.
- Artifacts, including the script and transcripts, land in `~/.pi/agent/workflows/<runId>/`.

There is no resume — a failed run is re-run.
