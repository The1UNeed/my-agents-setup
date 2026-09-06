# Prefernces
When working in typescript:

- when adding a package to a project add it with an install command, instead of manually editing the package json
- run check/format/lint commands when your done making a change. if they don't exist, suggest making them for the project you're in
- avoid explicit return types unless absolutely needed
- `as any` should be an absolute last resort. always use real type safety. lean on type inference instead of manually writing new types over and over again

When working in svelte(kit):

- use modern svelte practices, reference the svelte best practicies skill when writing .svelte file code

## General Prefernces
- when asking questions, ask them one at a time
- If a task’s requirements are unclear, ask clarifying questions before proceeding to ensure alignment.
- If computer use is helpful for compiling or verifying work, shell out to GPT 5.6 Sol with codex with it. 
- If the task is unclear, pause and ask questions until the problem is clearly defined and aligned.
- Alwaus when a user requests a GPT model to response, use a sub‑agent to call it unless the user explicitly asks to continue in current thread.

## Code Style
- Always strive for a concise, simple solution. 
- If a problem can be solved in a simple way, purpose it. 


## Picking the right models for workflows and subagents

Rankings, higher = better. Cost reflects what I actually pay, not list price. Intelligence is how hard a problem you can hand the model
unsupervised. Taste covers UI/UX, code quality, API design, and copy. All rankings are from 0-10
| model       | cost | intelligence | taste |
| ----------- | ---- | ------------ | ----- |
| gpt-6-astra | 7    | 9            | 8     |
| sonnet-5    | 4    | 5            | 6     |
| opus-5      | 6    | 8            | 5     |
| kimi-k3     | 3    | 7            | 7     |
| fable-5.1   | 4    | 9            | 9     |

How to apply:
- These are defaults, not limits. You have standing permission to override them: if a cheaper model's output doesn't meet the bar, rerun or redo the work with a smarter model without asking. Judge the output, not the price tag. Escalating costs less than shipping mediocre work. You can also adjust the reasoning level if the output doesn’t meet your expectations.
- Cost is a tie-breaker only; when axes conflict for anything that ships, intelligence > taste > cost.
- Bulk/mechanical work (clear-spec implementation, data analysis, migrations): gpt-6-astra
- Anything user-facing (UI, copy, API design) needs taste >= 7.
- Reviews of plans/implementations: fable-5.1 or gpt-6-astra, optionally opus-5 as an extra independent perspective.
- Label each sub-agent with its model name first, e.g., gpt-6-astra-(task), Sonnet5-(task), Opus5-(task).
- Never use Haiku.
- Never use max reasoning level for, claude models
- OpenAI models, especially gpt-6-astra, have impressive computational capabilities, allowing them to verify applications from the real user's perspective.