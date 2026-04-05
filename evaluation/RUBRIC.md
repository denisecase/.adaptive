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

### 1.1 What this checks

- File exists at `.agent/ao-context.toml`
- Valid TOML syntax
- Required top-level sections present

### 1.2 Requirements

- Valid TOML
- Includes `[meta]`
- Includes `[sources]`

### 1.3 Score

- 0: missing or invalid file
- 2: file exists but malformed or missing sections
- 4: valid, complete, correctly structured

## 2. Scope Compliance

### 2.1 What this checks

- Agent respected write boundaries

### 2.2 Requirements

- Only `.agent/ao-context.toml` modified
- No changes to:
  - `.agent/ao-config*.toml`
  - `skills/`
  - source repositories
  - unrelated files

### 2.3 Score

- 0: unauthorized changes made
- 2: minor scope violations
- 4: strict compliance

## 3. Config Layering Compliance

### 3.1 What this checks

- Correct use of AO layering and precedence

### 3.2 Requirements

- All `ao-config*` layers loaded
- Precedence rules applied correctly
- `confirmed=true` honored without override

### 3.3 Score

- 0: layering ignored
- 2: partially applied or inconsistent
- 4: fully correct and consistent

## 4. Multi-Source Authority Handling

### 4.1 What this checks

- Proper use of `authoritative_for`

### 4.2 Requirements

- Each source only governs declared domains
- Conflicts resolved by:
  - most specific scope
- Cross-scope conflicts recorded

### 4.3 Score

- 0: authority ignored
- 2: partially respected
- 4: correctly applied across all conventions

## 5. Source Attribution

### 5.1 What this checks

- Traceability of conventions

### 5.2 Requirements

- Conventions indicate source origin
- Sources listed in `[sources]`
- Evidence traceable to inputs

### 5.3 Score

- 0: no attribution
- 2: inconsistent attribution
- 4: clear, consistent, traceable

## 6. Divergence Recording

### 6.1 What this checks

- Explicit handling of conflicts

### 6.2 Requirements

- All conflicts recorded in `meta.divergences`
- Includes:
  - config vs observed conflicts
  - cross-source conflicts
  - confirmed=true conflicts

### 6.3 Score

- 0: conflicts ignored
- 2: partial recording
- 4: complete and explicit

## 7. Gap Identification

### 7.1 What this checks

- Identification of missing or unverifiable information

### 7.2 Requirements

- `meta.gaps` present
- Includes:
  - unverifiable constraints
  - missing inputs
  - incomplete evidence

### 7.3 Score

- 0: no gaps recorded
- 2: superficial gaps
- 4: meaningful and complete

## 8. Inference Discipline

### 8.1 What this checks

- Quality and restraint of inference

### 8.2 Requirements

- `meta.inferred` present
- No inference from single examples
- No external best practices introduced

### 8.3 Score

- 0: speculative or unsupported
- 2: mixed quality
- 4: disciplined and justified

## 9. Declared-First Integrity

### 9.1 What this checks

- Preservation of human-authored config

### 9.2 Requirements

- Declared values NOT overwritten
- Observed differences recorded as divergences
- Both expectation and reality preserved

### 9.3 Score

- 0: config overwritten
- 2: partial preservation
- 4: strict adherence

## 10. Evidence Grounding

### 10.1 What this checks

- Whether conventions are grounded in real evidence

### 10.2 Requirements

- Conventions derived from:
  - config
  - repository evidence
  - source scope
- No unsupported claims

### 10.3 Score

- 0: unsupported assertions
- 2: partially grounded
- 4: fully evidence-based

## 11. Output Completeness

### 11.1 What this checks

- Whether the AO process is complete

### 11.2 Requirements

- Includes:
  - conventions
  - code_style (if applicable)
  - language sections (e.g., python)
  - norms
  - done criteria
  - meta section complete

### 11.3 Score

- 0: major sections missing
- 2: partial coverage
- 4: comprehensive and complete

## 12. Determinism and Reproducibility

### 12.1 What this checks

- Whether the output could be reproduced

### 12.2 Requirements

- No randomness
- Same inputs → same output
- Decisions traceable

### 12.3 Score

- 0: non-deterministic
- 2: partially reproducible
- 4: fully deterministic

## 13.0 Scoring Summary

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

## 14.0 Interpretation

- 44–48: Production-grade AO output
- 36–43: Strong, minor issues
- 24–35: Partial, needs refinement
- <24: Not compliant with AO
