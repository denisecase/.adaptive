# Adaptive Onboarding (AO) Evaluation Rubric

This rubric evaluates the quality and correctness of a generated
`.agent/ao-context.toml` produced under the Adaptive Onboarding (AO) process.

Each criterion is scored from 0 to 4:

- 0 = missing / invalid
- 1 = weak / incorrect
- 2 = partial
- 3 = strong
- 4 = complete and correct

Total score is the sum across all criteria.

## 1. Output Validity

### What this checks

- File exists at `.agent/ao-context.toml`
- Valid TOML syntax
- Required top-level sections present

### Requirements

- Valid TOML
- Includes `[meta]`
- Includes `[sources]`

### Score

- 0: missing or invalid file
- 2: file exists but malformed or missing sections
- 4: valid, complete, correctly structured

## 2. Scope Compliance

### What this checks

- Agent respected write boundaries

### Requirements

- Only `.agent/ao-context.toml` modified
- No changes to:
  - `.agent/ao-config*.toml`
  - `skills/`
  - source repositories
  - unrelated files

### Score

- 0: unauthorized changes made
- 2: minor scope violations
- 4: strict compliance

## 3. Config Layering Compliance

### What this checks

- Correct use of AO layering and precedence

### Requirements

- All `ao-config*` layers loaded
- Precedence rules applied correctly
- `confirmed=true` honored without override

### Score

- 0: layering ignored
- 2: partially applied or inconsistent
- 4: fully correct and consistent

## 4. Multi-Source Authority Handling

### What this checks

- Proper use of `authoritative_for`

### Requirements

- Each source only governs declared domains
- Conflicts resolved by:
  - most specific scope
- Cross-scope conflicts recorded

### Score

- 0: authority ignored
- 2: partially respected
- 4: correctly applied across all conventions

## 5. Source Attribution

### What this checks

- Traceability of conventions

### Requirements

- Conventions indicate source origin
- Sources listed in `[sources]`
- Evidence traceable to inputs

### Score

- 0: no attribution
- 2: inconsistent attribution
- 4: clear, consistent, traceable

## 6. Divergence Recording

### What this checks

- Explicit handling of conflicts

### Requirements

- All conflicts recorded in `meta.divergences`
- Includes:
  - config vs observed conflicts
  - cross-source conflicts
  - confirmed=true conflicts

### Score

- 0: conflicts ignored
- 2: partial recording
- 4: complete and explicit

## 7. Gap Identification

### What this checks

- Identification of missing or unverifiable information

### Requirements

- `meta.gaps` present
- Includes:
  - unverifiable constraints
  - missing inputs
  - incomplete evidence

### Score

- 0: no gaps recorded
- 2: superficial gaps
- 4: meaningful and complete

## 8. Inference Discipline

### What this checks

- Quality and restraint of inference

### Requirements

- `meta.inferred` present
- No inference from single examples
- No external best practices introduced

### Score

- 0: speculative or unsupported
- 2: mixed quality
- 4: disciplined and justified

## 9. Declared-First Integrity

### What this checks

- Preservation of human-authored config

### Requirements

- Declared values NOT overwritten
- Observed differences recorded as divergences
- Both expectation and reality preserved

### Score

- 0: config overwritten
- 2: partial preservation
- 4: strict adherence

## 10. Evidence Grounding

### What this checks

- Whether conventions are grounded in real evidence

### Requirements

- Conventions derived from:
  - config
  - repository evidence
  - source scope
- No unsupported claims

### Score

- 0: unsupported assertions
- 2: partially grounded
- 4: fully evidence-based

## 11. Output Completeness

### What this checks

- Whether the AO process is complete

### Requirements

- Includes:
  - conventions
  - code_style (if applicable)
  - language sections (e.g., python)
  - norms
  - done criteria
  - meta section complete

### Score

- 0: major sections missing
- 2: partial coverage
- 4: comprehensive and complete

## 12. Determinism and Reproducibility

### What this checks

- Whether the output could be reproduced

### Requirements

- No randomness
- Same inputs → same output
- Decisions traceable

### Score

- 0: non-deterministic
- 2: partially reproducible
- 4: fully deterministic

## Scoring Summary

| Criterion                     | Score (0–4) |
| ----------------------------- | ----------- |
| 1. Output Validity            |             |
| 2. Scope Compliance           |             |
| 3. Config Layering Compliance |             |
| 4. Multi-Source Authority     |             |
| 5. Source Attribution         |             |
| 6. Divergence Recording       |             |
| 7. Gap Identification         |             |
| 8. Inference Discipline       |             |
| 9. Declared-First Integrity   |             |
| 10. Evidence Grounding        |             |
| 11. Output Completeness       |             |
| 12. Determinism               |             |
| **Total (out of 48)**         |             |

## Interpretation

- 44–48: Production-grade AO output
- 36–43: Strong, minor issues
- 24–35: Partial, needs refinement
- <24: Not compliant with AO
