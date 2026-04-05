# .adaptive

[![Adaptive Fetch Skills](https://github.com/denisecase/.adaptive/actions/workflows/adaptive-fetch-skills.yml/badge.svg?branch=main)](https://github.com/denisecase/.adaptive/actions/workflows/adaptive-fetch-skills.yml)
[![Adaptive Refresh Context](https://github.com/denisecase/.adaptive/actions/workflows/adaptive-refresh-context.yml/badge.svg?branch=main)](https://github.com/denisecase/.adaptive/actions/workflows/adaptive-refresh-context.yml)
[![Markdown Lint](https://github.com/denisecase/.adaptive/actions/workflows/md-lint.yml/badge.svg?branch=main)](https://github.com/denisecase/.adaptive/actions/workflows/md-lint.yml)
[![Check Links](https://github.com/denisecase/.adaptive/actions/workflows/links.yml/badge.svg?branch=main)](https://github.com/denisecase/.adaptive/actions/workflows/links.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/license/MIT)
[![Dependabot](https://img.shields.io/badge/Dependabot-enabled-brightgreen.svg)](https://github.com/denisecase/.adaptive/security)

> Maintaining project conventions for agent-assisted engineering

Uses the methodology defined in  
[Adaptive Interfaces](https://github.com/adaptive-interfaces),  
which provides engineering discipline for designing agent context to produce consistent, convention-aligned output.

Conventions are specified in [.agent/](./.agent/):

- Some are hand written (`ao-config*.toml`)
- Some are agent written (`ao-context*.toml`)
-

## This Project

This project fetches current versions of key
adaptive-interfaces skills and uses them
to define, maintain, and update conventions.

- Vendored skill inputs stored in `skills/<skill-name>/SKILL.md`
- Hand-written conventions stored in `.agent/ao-config.toml`
- Agent-assessed conventions stored in `.agent/ao-context.toml`

## Agent Options for Updating Conventions

### Claude Code (works)

Included with a Claude Pro subscription ($20/month).
Terminal CLI that reads your repo, makes real HTTP calls, writes files,
and executes commands.
This is a good tool for running the update.

Install:

```shell
npm install -g @anthropic-ai/claude-code
```

### OpenAI Codex CLI (ChatGPT)

Terminal CLI that can inspect your repository, edit files, and run commands.
You can authenticate with your ChatGPT account or an OpenAI API key.
This is a good tool for generating or refreshing ao-context\*.toml
from vendored skills and local repository evidence.
NOT included with a Plus subscription ($20/month).
API usage is billed separately from ChatGPT subscriptions
when you use an API key.

Install:

```shell
npm install -g @openai/codex
```

### GitHub Copilot

Good for inline code suggestions while editing.
Not suitable for full convention refresh workflows that need
deep repository inspection, file writes, and controlled artifact generation.

## Run Requirements (Chat is not enough)

Updates require an agent with:

- file system access (read/write repository files)
- ability to follow multi-step instructions
- sufficient context window to process skills + repo state

Chat-based interfaces are useful for reviewing and refining skills,
but cannot execute updates because they do not operate on your local file system.

## Run Update (with Claude Code)

From the repository root, start a session:

```shell
claude
```

Select your authorization (e.g. Option 1) and it will open a browser to authenticate against your pro account.
After auth is successful, follow the terminal prompts, e.g. "Login successful. Press Enter to continue…".
Accept the notice (hit Enter).
When asked " Use Claude Code's terminal setup?" chose your option (e.g., 1 or Enter to confirm).
When asked "Is this a project you created or one you trust?", chose your option (e.g., 1 or Enter to confirm).
Then in the session, paste the instructions:

```shell
─────────────────────────────────────────────────────────────────────────
❯ Read ./skills/adaptive-conformance-specification/SKILL.md
then read ./skills/adaptive-onboarding/SKILL.md
then follow prompt.md
─────────────────────────────────────────────────────────────────────────
```

Read questions and answer or Hit Enter to accept default until done.
After the response finishes:

1. Review any new .agent/ao-context\*.toml files.

## Run Update (with OpenAI Codex)

From the repository root, start a session:

```shell
codex
```

Authenticate when prompted:

- Sign in with your ChatGPT account, or
- Provide an OpenAI API key

Follow the terminal prompts until you reach the interactive session.
Then at the Codex prompt, paste the instructions

```shell
Read ./skills/adaptive-conformance-specification/SKILL.md
then read ./skills/adaptive-onboarding/SKILL.md
then read ./prompt.md
then execute the AO process to generate or update .agent/ao-context.toml

Constraints:
- Treat .agent/ao-config*.toml as authoritative and read-only
- Only write or modify .agent/ao-context*.toml
- Do not modify source files outside .agent/
- Prefer existing repository patterns over external assumptions
```

### During execution

- Answer clarification questions if prompted
- Otherwise press Enter to accept defaults

The agent should:

1. Read vendored skills from `skills/`
2. Inspect the repository structure and conventions
3. Reconcile declared conventions (`ao-config`) with observed patterns
4. Generate or update `.agent/ao-context.toml`

### After completion

Review outputs carefully:

- Inspect changes in `.agent/ao-context.toml`
- Confirm alignment with declared conventions in `.agent/ao-config.toml`
- Reject or adjust any incorrect inferences

Suggested workflow:

```shell
git diff .agent/
```

If acceptable:

```shell
git add .agent
git commit -m "chore: refresh agent context"
```

### Notes

- `ao-config*.toml` is human-authored and authoritative
- `ao-context*.toml` is agent-generated and replaceable
- Do not allow agents to overwrite config files
- Regenerate context periodically or when conventions change

## Human-Written Config and Agent-Written Context

This repository separates declared conventions from agent-generated observations.

- `ao-config-*.toml` = human-written
- `ao-context-*.toml` = agent-written

The `ao-config` files are the authoritative record of intent.  
They capture declared conventions, preferences, and alignment stance.

The `ao-context` files are generated by an agent from observed evidence in one or more repositories.  
They record what the agent inferred and any gaps or uncertainties.

Agent-written context must not overwrite human-written config.  
Keep both.

When both files exist for the same layer, load both at session start.  
On conflicts, `ao-config-*.toml` takes precedence.  
Differences between the two are useful because they surface gaps between declared intent and observed practice.

## Recommended Process

1. Write and maintain `ao-config-*.toml` by hand.
2. Fetch current skills into `skills/`.
3. Run an agent CLI to scan the repository or repositories.
4. Write generated output only to `ao-context-*.toml`.
5. Review differences between `ao-config-*.toml` and `ao-context-*.toml`.
6. Update hand-written config when conventions should change.
7. Regenerate context after meaningful repository changes.

## Session Initialization

Load initiation materials at the start of an agent session before any task.

Start with ACS, then AO, then local initiation files.

```text
Read ACS SKILL.md
then read AO SKILL.md
then read any ao-config*.toml and ao-context*.toml files present in .agent/
then begin work on <your task>
```

## Agent CLI Options

### Option 1: Claude Code

Use Claude Code to read ACS, AO, and local `.agent/` files, then perform the task.

```text
Read ACS SKILL.md
then read AO SKILL.md
then read any ao-config*.toml and ao-context*.toml files present in .agent/
then begin work on <your task>
```

### Option 2: OpenAI CLI

Use an OpenAI CLI in the same conceptual way.

The goal is the same:  
load the skills and local initiation files first,  
then generate or refine `ao-context-*.toml` without overwriting `ao-config-*.toml`.

```text
Load ACS SKILL.md
then load AO SKILL.md
then load any ao-config*.toml and ao-context*.toml files present in .agent/
then inspect the repository
then write or update only ao-context-*.toml
then begin work on <your task>
```

## Evaluation Workflow

When testing agent generation quality:

- keep the human-written `ao-config` files unchanged
- generate a candidate `ao-context` file with an agent CLI
- compare the generated result against the manually maintained version
- review where the agent inferred correctly, missed conventions, or overfit local evidence
- use those findings to improve prompts, workflow, or local config

## GitHub Actions

- `.github/workflows/adaptive-fetch-skills.yml` - fetch latest upstream skills
- `.github/workflows/adaptive-refresh-context.yml` - regenerate `.agent` context artifacts

## Developer Maintenance

```shell
npx markdownlint-cli@latest "**/*.md"
```

## Annotations

[ANNOTATIONS.md](./ANNOTATIONS.md)

## Manifest

[MANIFEST.toml](./MANIFEST.toml)

## Citation

[CITATION.cff](./CITATION.cff)

## License

[MIT](./LICENSE)
