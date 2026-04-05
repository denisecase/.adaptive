---
name: adaptive-onboarding
description: >
  Scans one or more repositories to generate ao-context.toml,
  a persistent Team Context Document in the AO schema.
  Activate when ao-context.toml is missing or stale,
  or when agent output does not match team norms and no
  human-authored ao-config.toml exists to explain why.
---

# Adaptive Onboarding (AO) Generation Skill

This skill generates `ao-context.toml` by scanning repositories
and recording observed conventions in the AO schema.

It is one way to produce an AO-format file.
A human-authored `ao-config.toml` is equally valid and often more accurate.
See the [README](./README.md) for the schema and the full context.

This skill operates under the Adaptive Conformance Specification (ACS).
Apply ACS discovery and conformance steps before executing any
domain-specific actions below.

## 1. Scope

### 1.1. What this skill does

- Scans one or more repositories for observable conventions
- Interprets evidence from config files, CI workflows, and code patterns
- Produces `ao-context.toml` grounded in observed evidence
- Records gaps, inferences, and best-practice divergences explicitly
- Optionally loads `ao-config.toml` as a prior if one exists

### 1.2. What this skill does not do

- Author `ao-config.toml`. That is a human responsibility.
- Override `ao-config.toml` entries. Those are human-declared conventions.
  They take precedence over observed ones on any field where they conflict.
- Invent conventions not grounded in observed evidence.
- Cover tool capability mapping - see Adaptive Tool Discovery (ATD).
- Govern observation and conformance discipline - that is ACS.

### 1.3. Relationship to ao-config.toml

AO config files follow a layered loading order:

1. `ao-config.toml` — org-wide defaults
2. `ao-config-{language}.toml` — language-specific additions, if present
3. `ao-config-{language}-{type}.toml` — type-specific extensions, if present

Files may be loaded in any order.
Apply layering priority after all files are loaded,
not during loading.

Later layers override earlier ones on any field where they conflict.
Conflicts between layers are recorded in `meta.divergences`,
not silently resolved.

For each layer present, load it before scanning.
Use its entries as a prior:
fields marked `confirmed = true` are accepted without re-inference.
Fields marked `confirmed = false` are verified against observed patterns.

After scanning, compare generated entries to declared entries.
Record any conflict in `divergences` with both values and their sources.
Do not silently resolve conflicts in either direction.

## 2. Sources

The skill scans the sources declared in `ao-config.toml` under `[sources]`
if that section exists.
If no sources are declared, scan the current repository.

Sources may be local paths or remote URLs.
Each source carries an `authoritative_for` list that scopes its authority
to specific concerns (e.g., `["packaging", "ci"]`).
A source that is authoritative for packaging does not override
observed test conventions from a different source.

If no `authoritative_for` is declared, treat the source as
authoritative for all concerns, weighted below any source
that does declare explicit scope.

## 3. Workflow

Follows ACS workflow steps with the following AO-specific guidance.

### 3.1. Discover

Determine which config layers apply to this repository
by reading `[repo].type` and `primary_language` from `ao-config.toml`.
Load all applicable layers before scanning any source.

For each source, enumerate:

- AO config layers: `ao-config.toml`,
  `ao-config-{language}.toml`,
  `ao-config-{language}-{type}.toml`
- Repository structure: top-level layout, major directories, key files
- Explicit documentation: README, CONTRIBUTING, DECISIONS, ADRs,
  style guides, wiki pages
- Configuration files: pyproject.toml, ruff.toml, .editorconfig,
  linter configs, formatter configs, CI workflows, pre-commit hooks
- Code patterns: naming conventions, module structure, test organization,
  error handling style, function size, complexity, docstring style

Record what you find per source.
Do not proceed until discovery is sufficient to characterize
how this team works.

### 3.2. Interpret

For each convention, determine the strongest signal across all sources
weighted by `authoritative_for` scope.

Assess alignment with community best practices:

- If observed practice matches best practice: record as confirmed
- If observed practice diverges: record the divergence, note the
  best practice, and apply `best_practices_stance` to determine
  what goes in the generated file
- Do not silently conform to a divergent practice without recording it
- Do not silently override an observed practice in favor of best practice

Identify gaps: conventions that are inconsistent, undocumented,
or inferred rather than directly observed.
State gaps explicitly; do not resolve them by assumption.

### 3.3. Select

Where conventions conflict across sources, resolve by priority:

  ao-config-{language}-{type}.toml (confirmed=true) >
  ao-config-{language}.toml (confirmed=true) >
  ao-config.toml (confirmed=true) >
  CI enforcement >
  explicit config file >
  style guide >
  code patterns

State the selected value and the evidence that justifies it.
Record all conflicts in `meta.divergences` regardless of how they resolve.

### 3.4. Execute

Produce `ao-context.toml` using the AO schema.
Include only what is grounded in observed evidence.
Mark inferred conventions in `meta.inferred`.
Record best-practice divergences in `meta.divergences`.
Do not invent norms or generalize from single examples.

Set `meta.produced_by` to the agent identifier and
`meta.produced_on` to today's ISO date.

### 3.5. Validate

Before storing, verify:

- Each convention is traceable to an observed source
- Gaps and inferred items are recorded in `meta`
- Best-practice divergences are recorded with stance applied
- No external conventions have been silently introduced
- No ao-config.toml entries have been silently overridden

If validation fails, record the failure in `meta.gaps`.
Do not silently omit gaps or substitute assumptions.

### 3.6. Conclude

Create `.agent/` if it does not exist
Store `ao-context.toml` at `.agent/ao-context.toml`.
State any conventions that could not be determined and why.
State what additional access or documentation would resolve those gaps.
Score output against the rubric in `evaluation/rubric.md` if present.

## 4. Refresh

Regenerate `ao-context.toml` when:

- team conventions change
- significant new tooling is adopted
- codebase structure is reorganized
- agent output stops matching team norms

See [.github/workflows/refresh-context.yml](.github/workflows/refresh-context.yml) for the scheduled refresh workflow.

Small changes (new norm, ownership update): auto-commit.
Large changes (alignment stance, definition of done, major restructure):
open a PR for human review before merging.

## 5. Error Handling

Follows ACS section 5 (Error Handling).

AO-specific additions:

- If documentation contradicts observed code patterns,
  record both and flag the discrepancy explicitly
- If ownership cannot be determined, record as `"unowned"` with a note
- If conventions are inconsistent across the codebase,
  record the dominant pattern and note the inconsistency
- If a best-practice divergence cannot be classified under the stated stance,
  record it in `divergences` and flag for human review

## 6. Stopping Conditions

Follows ACS section 6 (Stopping Conditions).

AO-specific additions:

- Stop if no source repository can be read with available credentials
- Stop if no documentation or code patterns can be found to ground conventions
- Report what context was established, what could not be determined,
  and what access would be needed to proceed

## 7. Invocation by Other Skills

Skills that require team context should load the AO file directly
rather than invoking this generation skill:

```text
Read .agent/ao-config.toml and .agent/ao-context.toml
then begin work on <task>
```

Invoke this skill only when the context file is missing or stale
and needs to be regenerated from source repositories.

Skills that depend on AO for generation include this in their preamble:

> This skill operates under Adaptive Onboarding (AO).
> If ao-context.toml is missing, run AO to generate it before proceeding.

AO governs team context generation.
ACS governs observation and conformance discipline.
ATD governs tool capability mapping.
All three apply when an agent must work as a team member
within a project that uses external tools.
