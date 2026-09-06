# Personal Preferences

## Code Style

- Always strive for a concise, simple solution. 
- If a problem can be solved in a simple way, purpose it. 

## General Prefernces

- If a task’s requirements are unclear, ask clarifying questions before proceeding to ensure alignment.
- If computer use is helpful for compiling or verifying work, shell out to GPT 5.6 Sol with codex with it. 
- If the task is unclear, pause and ask questions until the problem is clearly defined and aligned.
- Alwaus when a user requests a GPT model to response, use a sub‑agent to call it unless the user explicitly asks to continue in current thread.

## Picking the right models for workflows and subagents

Rankings, higher = better. Cost reflects what I actually pay not list price. Intelligence is how hard a problem you can hand the model
unsupervised. Taste covers UI/UX, code quality, API design, and copy.

| model       | cost | intelligence | taste |
| ----------- | ---- | ------------ | ----- |
| gpt-6-astra | 7    | 9            | 7     |
| opus-5      | 5    | 6            | 7     |
| fable-5.1   | 4    | 9            | 9     |

How to apply:

- These are defaults, not limits. You have standing permission to override them: if a cheaper model's output doesn't meet the bar, rerun or redo the work with a smarter model without asking. Judge the output, not the price tag. Escalating costs less than shipping mediocre work.
- Cost is a tie-breaker only; when axes conflict for anything that ships, intelligence > taste > cost.
- Bulk/mechanical work (clear-spec implementation, data analysis, migrations): gpt-6-astra
- Anything user-facing (UI, copy, API design) needs taste >= 7.
- Reviews of plans/implementations: fable-5.1 or opus-5, optionally gpt-6-astra as an extra independent perspective.
- Label each sub-agent with its model name first, e.g., GPT5.6-Sol-(task), Sonnet5-(task), Opus5-(task).
- Never use Haiku.
- Mechanics: gpt-6-astra is only reachable through the Codex CLI - `codex exec` / `codex review` (my `~/.codex/config.toml` defaults to gpt-6-astra). Use the codex-implementation, codex-review, and codex-computer-use skills; for work they don't cover (investigation, data analysis), run `codex exec -s read-only` directly with a self-contained prompt.
- Claude models (sonnet-5, opus-5, fable-5.1) run via the Agent/Workflow model parameter.

Using gpt-6-astra inside workflows and subagents (the model parameter only takes Claude models, so use a wrapper):

- Spawn a thin Claude wrapper agent with `model: 'sonnet 5', effort: 'low'` whose prompt instructs it to write a self-contained codex prompt, run `codex exec` via Bash.
- The appropriate way to execute GPT 5.6 is to use a sub‑agent wrapper with `model: 'sonnet 5', effort: 'low'`. Even for a single task that should be done with gpt-6-astra you should spawn a sub‑agent.
