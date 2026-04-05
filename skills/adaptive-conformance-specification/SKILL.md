---
name: adaptive-conformance-specification
description: >
  Use to generate code, tests, workflows, or artifacts
  that conform to an unfamiliar codebase or tool surface.
  Activate when working in an unfamiliar repository,
  calling an undocumented API or CLI,
  or generating files that must match local conventions.
  Inspection is required before action.
---

# Adaptive Conformance Specification (ACS)

An operating specification for agents working within
unfamiliar codebases or tool surfaces.
The agent's job is to learn the system from the system itself,
then act in conformance with what it observes,
never from assumptions or external preferences.

## 1. Core Principles

### 1.1. Evidence First

Every action must be grounded in observed evidence:
schemas, examples, executed outputs, or file contents.
Do not infer behavior that has not been demonstrated.

### 1.2. Inspect Before Acting

Always examine the relevant surface before generating output.
This is not optional and is not skippable under time pressure.

### 1.3. Minimal Commitment

When evidence is incomplete, prefer a partial correct conclusion
over a full incorrect one.
State uncertainty explicitly.

### 1.4. Conform to Local Patterns

Match what already exists:

- naming conventions (files, functions, variables, tests)
- structural patterns (directory layout, module organization)
- sequencing patterns (import order, setup/teardown, error handling style)
- tooling idioms (how the project uses its own abstractions)

Do not introduce outside conventions unless explicitly instructed,
or unless local evidence is missing or internally inconsistent.

## 2. Workflow

Follow these steps in order.
Do not bypass the evidentiary function of a step.
What varies by task is how much inspection is sufficient,
not whether inspection happens.

### 2.1. Discover

Enumerate the available surface:

- List relevant files, commands, or endpoints
- Identify names, parameters, input types, output structures
- Read at minimum:
  - repository root structure (top-level directories and files)
  - configuration files (e.g., pyproject.toml, package.json, Makefile)
  - at least three representative existing files of the artifact type being produced

Record what you find.
Do not proceed until discovery is sufficient
for the current artifact and task.

### 2.2. Interpret

Determine the purpose and constraints of each relevant component:

- What does this tool/function/module do?
- What inputs are required vs. optional?
- What constraints or valid ranges apply?
- What does a well-formed output look like?

Identify the dominant local pattern for the artifact
type you are about to produce.
If multiple patterns exist, note the variation
and choose the most prevalent one, stating your choice.

### 2.3. Select

Choose the specific tool, template, or approach you will use.
State your selection and explicitly reference the observed patterns
or files that justify it.
If the selection is ambiguous, say so.

### 2.4. Execute

Produce the artifact with valid, minimal inputs.
Do not add parameters, fields, or behaviors
not observed in the existing system.
Do not embellish.

### 2.5. Validate

Check the output before presenting it:

- Is the structure consistent with observed examples?
- Are naming conventions matched?
- Are there no introduced assumptions?
- Are there no fields, parameters, or behaviors not observed in the system?

If validation fails:

- Record the failure
- Identify the cause
- Do not silently retry with guessed corrections

### 2.6. Conclude

Provide:

- The result
- The evidence it is based on
- Any remaining uncertainty

## 3. Conformance Output Contract

ACS governs behavior, but that behavior must be made visible.
Every ACS-compliant execution MUST produce a structured conformance record.
All components defined below are REQUIRED unless explicitly stated otherwise.
Omission of any required component constitutes non-conformance.

### 3.0. Contract Requirements

- Each component must be present
- Each claim must be traceable to observed evidence
- Omitted sections are considered non-conformant
- Fabricated or inferred content without evidence is a violation
- No content may be included that is not supported by observed evidence

The record MUST include the following components.

### 3.1. Observed Evidence

A minimal, explicit account of what was inspected:

- files, commands, endpoints, or examples examined
- relevant structures (inputs, outputs, schemas, patterns)

This must be selective and evidence-based, not a raw dump.

### 3.2. Interpretation

A concise account of how the system was understood:

- purpose of relevant components
- required vs optional inputs
- constraints and expected outputs
- identified local patterns

### 3.3. Selection Rationale

The chosen approach and why:

- which tool, template, or pattern was selected
- which observed evidence supports that selection
- any ambiguity or competing patterns

### 3.4. Generated Artifact

The produced output.

Constraints:

- must conform to observed system patterns
- must not introduce unobserved fields, parameters, or behavior
- must be minimal and valid

### 3.5. Validation Check

A self-check against observed patterns:

- structure matches existing examples
- naming and conventions are consistent
- no unsupported assumptions introduced

If validation fails, the failure must be reported, not corrected silently.

### 3.6. Uncertainty and Limits

Explicit statement of:

- what is known
- what is uncertain
- what additional evidence would be required to proceed further

## 4. Pattern Inference

The invoking skill defines which concerns to extract and where to find them.
ACS governs how to record, apply, and validate those observations,
not what to look for.

## 5. Error Handling

When a tool call, execution, or output produces an error or unexpected result:

- Record the error with context
- Do not ignore it
- Do not silently modify inputs and retry
- Do not assume the error is transient

Instead:

- Report what happened
- State what was expected vs. what was observed
- Suggest the next grounded step, if one exists

## 6. Stopping Conditions

Stop and report rather than proceeding when:

- Required information cannot be obtained from the system
- Tool behavior cannot be safely inferred from evidence
- Outputs are inconsistent in ways that cannot be explained

At stop, report:

- What is known
- What is unknown
- What would be needed to proceed

Do not fabricate completion.

## 7. Prohibited Behaviors

The agent must never:

- Hallucinate tool names, parameters, file paths, or outputs
- Assume behavior that has not been observed or documented
- Apply external frameworks or style preferences over local ones
- Override observed system patterns based on general best-practice reasoning
- Silently correct inputs to recover from errors

## 8. Invocation by Other Skills

Skills that invoke ACS should state this in their preamble:

> This skill operates under the Adaptive Conformance Specification (ACS).
> Apply ACS discovery and conformance steps before executing any
> domain-specific actions below.

ACS governs the observation and conformance layer.
The invoking skill governs the domain-specific generation layer.
Keep these concerns separate.
