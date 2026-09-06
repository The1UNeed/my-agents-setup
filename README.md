# Agent skills

This repository is my working collection of reusable instructions for coding agents. The skills cover recurring jobs such as filing and monitoring pull requests, uploading files, reviewing work with multiple models, provisioning machines, and reporting usage across a development fleet.

Most skills are written to work across several agent harnesses. Machine-specific and harness-specific instructions live in separate directories so they are installed only where they make sense.

## Repository layout

```text
.
├── universal/       Skills shared across supported machines and harnesses
├── command-center/  Skills used only from the machine that manages the fleet
└── claude-only/     Skills that depend on Claude Code behavior or data formats
```

Each skill has its own directory and a `SKILL.md` file. The frontmatter in that file declares where the skill can run and what it needs. The body contains the workflow the agent follows when the skill is triggered.

### Universal skills

`universal/` contains project-level workflows that remain useful on any development machine. Current examples include:

- `arena`, which runs several candidate implementations and combines the strongest parts.
- `babysit-pr`, which monitors an open pull request through CI and review.
- `file-pr`, which reviews the branch and opens a concise pull request.
- `file-upload`, which uploads a local artifact and returns a permanent URL.
- `grilling`, which stress-tests a plan or decision through structured questions.
- `html-communication`, which presents a report or specification as an HTML document.
- `interrogate`, which sends a change through independent model reviews.
- `myhtmls-read`, which reads private or public `myhtmls.dev` documents.
- `unslop`, which removes stock AI phrasing from prose.

See [universal/README.md](universal/README.md) for the placement rules and current inventory.

### Command-center skills

`command-center/` contains operational skills for the leader machine. These skills can inspect or change other machines, so they should not be copied across the fleet.

The current set covers fleet usage reports, machine inventory, and provisioning. See [command-center/README.md](command-center/README.md) for installation scope and safety notes.

### Claude-only skills

`claude-only/` is reserved for workflows tied to Claude Code internals, such as its transcript format, tool names, or session layout. See [claude-only/README.md](claude-only/README.md) for the boundary between Claude-specific and universal skills.

## Skill metadata

Most `SKILL.md` files begin with YAML frontmatter like this:

```yaml
---
name: file-upload
description: Upload a local file and return its public URL.
metadata:
  harness: [claude, codex]
  platform: [darwin, linux]
  scope: fleet
  requires: "FILE_HOST_TOKEN in the environment"
---
```

The fields have distinct jobs:

- `name` is the skill's invocation name.
- `description` tells the agent when to use it.
- `metadata.harness` lists compatible agent harnesses. Use `all` only when the workflow has no harness-specific assumptions.
- `metadata.platform` lists supported operating systems.
- `metadata.scope` separates fleet-wide skills from command-center-only operations.
- `metadata.requires` records external commands, credentials, services, or connectivity the workflow expects.

Some skills also set `disable-model-invocation: true`. Those skills run only when the user explicitly invokes them.

## Installing skills

Install only the directories appropriate for the target machine and harness:

- Put compatible universal skills in the harness's skills directory, such as `~/.codex/skills/` or `~/.claude/skills/`.
- Install command-center skills only on the leader machine.
- Install Claude-only skills only for Claude Code.

Before copying or linking a skill, read its metadata and check every item in `requires`. A skill may rely on a command-line tool, an environment variable, an authenticated service, or SSH access that is not available on every machine.

## Adding or changing a skill

1. Choose the narrowest correct directory: `universal/`, `command-center/`, or `claude-only/`.
2. Create a directory whose name matches the skill name.
3. Add a `SKILL.md` with a specific trigger description and accurate metadata.
4. Write the workflow as executable instructions. Include failure conditions and safety limits where the skill can affect remote systems or shared state.
5. Update the README in that directory when the skill inventory changes.
6. Test the skill in every harness and platform listed in its metadata.

Keep instructions concrete. If a workflow depends on a local path, credential, host inventory, or third-party command, state that dependency instead of assuming every machine has it.

## Credits

Several skills were adapted from or inspired by ideas in the following projects, then edited for this repository's workflows and writing conventions:

- [Cursor plugins](https://github.com/cursor/plugins)
- [Matt Pocock's skills](https://github.com/mattpocock/skills)
