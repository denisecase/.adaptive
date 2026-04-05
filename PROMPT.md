---
type: execution-prompt
name: adaptive-onboarding
purpose: >
  Execute Adaptive Onboarding (AO) to generate a Team Context Document
  from one or more repositories using explicit authority scoping.
role: >
  This file is an execution contract for agents.
  It defines what to read, how to reason, and what to produce.
scope: >
  Applies only to generation of `.agent/ao-context*.toml`.
  Does not modify source code, skills, or configuration inputs.
---

## 1.0 Execution Objective

Generate:

- `.agent/ao-context.toml`

This document must:

- reflect declared configuration (`ao-config*.toml`)
- incorporate repository evidence
- respect multi-source authority scoping (`authoritative_for`)
- record divergences and gaps explicitly



## 2.0 Governing Specifications

You MUST follow:

- Adaptive Conformance Specification (ACS)
- Adaptive Onboarding (AO)

You MUST:

- follow ACS: discovery → interpretation → selection → execution → validation
- follow AO: layering, precedence, divergence recording



## 3.0 Inputs (Read-Only)

### 3.1 Skills

- `./skills/adaptive-conformance-specification/SKILL.md`
- `./skills/adaptive-onboarding/SKILL.md`

### 3.2 Declared Configuration

- `.agent/ao-config.toml`
- `.agent/ao-config-*.toml` (all layers)

### 3.3 Scenario Definition

Use the sources defined below:

```toml
[[sources.repos]]
url = "https://github.com/denisecase/datafun-toolkit"
authoritative_for = ["packaging", "pyproject", "ci"]

[[sources.repos]]
url = "https://github.com/civic-interconnect/multidimensional-evaluation-engine"
authoritative_for = ["testing", "type_annotations", "docstrings"]

[[sources.repos]]
url = "https://github.com/denisecase/pro-analytics-02"
authoritative_for = ["documentation", "conventions"]
```

No target repository is specified.

This is an org-level context synthesis.



## 4.0 Outputs (Write Scope)

You MAY modify ONLY:

- `.agent/ao-context.toml`

You MUST NOT modify:

- `.agent/ao-config*.toml`
- `skills/`
- any source repositories
- any files outside `.agent/`



## 5.0 Core Rules

### 5.1 Separation of Responsibility

- `ao-config` = human-authored, authoritative
- `ao-context` = agent-derived, replaceable

Never override config.



### 5.2 AO Layering and Precedence

Apply precedence:

1. ao-config-{language}-{type} (confirmed=true)
2. ao-config-{language} (confirmed=true)
3. ao-config (confirmed=true)
4. CI enforcement
5. explicit config files
6. style guides
7. code patterns

Do NOT resolve conflicts silently.

Record all conflicts in `meta.divergences`.



### 5.3 Multi-Source Authority (CRITICAL)

Each source defines:

- `authoritative_for = [...]`

Rules:

- A source governs ONLY its declared domains
- Outside its domain, it has no priority
- If multiple sources apply:
  - the most specific scope wins
- If conflict remains:
  - record in `meta.divergences`
  - do not silently resolve



### 5.4 Declared-First Resolution

When conflict exists:

- declared config = expectation
- observed patterns = reality

You MUST:

- preserve both
- never overwrite declared values
- record divergence explicitly



### 5.5 Confirmed Fields

- `confirmed = true` → accept without re-inference
- `confirmed = false` → verify against evidence

Conflicts with confirmed=true MUST still be recorded.



### 5.6 Evidence Discipline

- infer only from repeated evidence
- do not generalize from single examples
- do not introduce external best practices



### 5.7 No Side Effects

You MUST NOT:

- modify code
- refactor structure
- introduce tooling
- generate recommendations

This is descriptive only.



## 6.0 Process

1. Load all `ao-config*` layers
2. Load all sources and their scopes
3. Collect observed evidence from each source
4. Apply ACS reasoning (internally only)
5. Resolve using:
   - layering
   - authority scoping
   - declared-first rules
6. Record:
   - conventions
   - inferred fields
   - divergences
   - gaps
7. Generate `.agent/ao-context.toml`



## 7.0 Output Requirements

Output MUST:

- be valid TOML
- include:

```toml
[meta]
produced_by = "<agent>"
produced_on = "<ISO date>"
inferred = []
divergences = []
gaps = []
```

- include `[sources]` with all input repositories
- attribute conventions to sources where applicable
- ensure all entries are traceable to evidence

Output MUST NOT include:

- explanations
- reasoning
- narrative text outside TOML comments



## 8.0 Completion Actions

After generating `.agent/ao-context.toml`:

1. Copy to:

```
evaluation/scenarios/multi-repo-authoritative-for/ao-context.toml
```

2. Write summary to:

```
evaluation/scenarios/multi-repo-authoritative-for/response.txt
```

3. Score against:

```
evaluation/rubric.md
```

Include score table in `response.txt`.



## 9.0 Completion Criteria

Complete when:

- `.agent/ao-context.toml` is generated
- sources are represented
- divergences are recorded
- inferred fields are marked
- no unauthorized files changed



## 10.0 Failure Conditions

Stop if:

- config layers missing
- sources cannot be accessed
- evidence insufficient

Record issues in `meta.gaps`.



## 11.0 Summary

This process:

- reads: skills + config + sources
- reasons: via ACS (internally)
- produces: AO context
- preserves: human authority
- records: divergence and uncertainty
- avoids: modification and assumption