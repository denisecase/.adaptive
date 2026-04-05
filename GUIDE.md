# Adaptive Guide

> How to Structure Agent Context for Team-Conforming Output

## The Problem

Agent output is often technically correct but not team-conforming.
The fix is better context, not a better model.
This guide specifies the required context.

No specialized background assumed.
The focus is on engineering practice, not model internals.

## Non-Goals

This is:

Not a tutorial on how to use Claude Code.
Not a prompt engineering cheat sheet.
Not about replacing engineers.

Engineers remain responsible for output quality.
Agents are instruments that require correct configuration
to produce correct output.

## Core Insight

Agents have no persistent memory of your team between sessions.
Every session starts from zero.
Without explicit context, the agent infers conventions
from training data which reflects industry averages,
not team-specific practices.

The context agents need is the same context
a new team member needs:
toolchain, structure, style, complexity, conventions,
definition of done, and domain knowledge.

Most teams have never written that context down.
Writing it down improves both agent output and team clarity.

## Part 1: The Problem

### 1.1 What goes wrong

- Agent output is correct but does not match team conventions
- Engineers spend time reformatting, renaming, restructuring
- The same corrections happen every session
- Different engineers get different agent behavior
- No one knows what context the agent has about the team

### 1.2 Why it goes wrong

Without explicit context,
agents infer conventions from training data.
Training data reflects industry averages.
Your team's specific practices are not in there.

### 1.3 The cost

- Review friction: PRs require style corrections unrelated to correctness
- Trust erosion: engineers stop relying on agent output
- Inconsistency: agent-generated code looks different from team-generated code
- Missed leverage: the agent could do more with better context

## Part 2: The Insight

### 2.1 Context is configuration

In this guide, **priors** are the structured, agent-readable form of that context.

An agent without team context will not produce team-conforming output.
This is not a model failure.
It is a configuration failure.
The fix is engineering, not prompting.

### 2.2 What context an agent needs

- Toolchain: what tools, what versions, what commands
- Structure: where code lives, how it is organized
- Style: naming, formatting, complexity, docstrings
- Conventions: what the team actually does vs what the docs say
- Definition of done: what a passing review looks like
- Domain knowledge: what correct means in this problem space

All of these can be made explicit; doing so **is** the work.

### 2.3 The side effect

Writing context for an agent
forces a team to articulate what it actually does.

Observed outcomes:

1. Agent output improves immediately
2. Team conventions turn out to be less consistent
   than anyone thought (which is itself useful)

The priors written for an agent
are often the clearest documentation
a team has ever produced about how it works.

## Part 3: The Framework

Three layers of context are required.
Each answers a different question.

### 3.1 ACS: How should the agent behave?

The Adaptive Conformance Specification defines
foundational agent behavior:
observe before acting,
infer from evidence,
conform to local patterns,
stop rather than guess.

This is not team-specific.
It applies to any agent working in any unfamiliar codebase.

→ [adaptive-conformance-specification](https://github.com/adaptive-interfaces/adaptive-conformance-specification)

### 3.2 ATD: What tools does the team use?

The Adaptive Tool Discovery skill maps
the capability surface of external tools:
MCP servers, APIs, CLIs, SDKs.

An agent that does not know what a tool actually does
will invoke it incorrectly or not at all.
ATD runs discovery once
and produces a persistent registry any session can load.

→ [adaptive-tool-discovery](https://github.com/adaptive-interfaces/adaptive-tool-discovery)

### 3.3 AO: How does the team work?

The Adaptive Onboarding skill captures team conventions
in a structured, agent-readable format:
toolchain, structure, style, complexity,
docstrings, ownership, definition of done,
and the norms that are never written down anywhere.

This is the layer most teams are missing entirely.

→ [adaptive-onboarding](https://github.com/adaptive-interfaces/adaptive-onboarding)

### 3.4 Domain context: What does correct mean here?

For domain-specific work, e.g., sensor data, financial models,
medical devices, scientific instrumentation,
agents also need to know what correct behavior looks like
in the problem domain.

Expected ranges, known failure modes, anomaly definitions,
calibration norms: context is captured in `ao-domain.toml`
alongside team conventions.
This functions as a fourth layer when domain correctness
cannot be inferred from code or tools alone.

## Part 4: The Practice

### 4.0 Loading order

```text
Read ACS            for foundational behavior
Read ATD registry   for tool capabilities
Read AO context     for team conventions
Read domain context for problem domain
Then begin work on <task>
```

Each layer narrows agent output
to what is correct for this team, these tools, this problem.

### 4.1 Skills vs prompt templates

A prompt template tells an agent what to say.
A skill specification tells an agent how to reason
and what evidence to require before acting.

A prompt template produces consistent phrasing.
A skill specification produces consistent reasoning.

Skill specifications constrain reasoning,
enabling consistent behavior across novel situations.

### 4.2 Writing priors an agent can act on

A prior is only useful if the agent can verify it,
apply it, or flag when it is violated.

Good prior entry:

```toml
max_function_lines = 30   # verified against observed code patterns
```

Weak prior entry:

```toml
code_quality = "high"     # agent cannot act on this
```

Rules for writing effective priors:

- Be specific enough that a violation is detectable
- Distinguish confirmed facts from inferred patterns
- Record where the convention came from
- Note when team practice diverges from best practice
  and what stance the team takes on the divergence

### 4.3 The confirmed flag

Entries in context files (for example `ao-config.toml`)
carry a `confirmed` flag.

`confirmed = true` -> agent accepts without re-inferring.
Use for stable toolchain facts.

`confirmed = false` -> agent verifies against observed patterns.
Use for style and complexity conventions that may vary by repo.

### 4.4 The best_practices_stance

When team conventions diverge from community best practices,
the agent must know what to do.

```toml
best_practices_stance = "balanced"
# prefer_team     # follow team; flag divergences
# balanced        # follow team; flag significant divergences for human review
# prefer_standard # follow best practices; flag team differences
```

This is a team decision, not an agent decision.
Making it explicit prevents silent conformance
or silent override.

### 4.5 Evaluating agent output

After loading priors and running a task, ask:

- Does the output match naming conventions?
- Does the output match structure conventions?
- Are functions the right size and complexity?
- Do docstrings follow the team style?
- Would this pass code review without style corrections?
- Does the output require human interpretation to resolve ambiguity?

If the answer to any of these is no,
the problem is in the priors.
Update them and re-run.

### 4.6 When to fork and tune the skills

The ACS, ATD, and AO skills in the adaptive-interfaces org
are starting points, not mandates.

Fork when:

- The team has stopping conditions ACS does not define
- The tool surface requires ATD extensions
- Team conventions require AO schema additions
- The domain requires a domain context file

Keep the core structure:
the six-step workflow,
the evidence-first principle,
the explicit gap recording.
These are the discipline, not the detail.

## Part 5: Worked Example

A simulated engineering team
works with temperature/frequency sensor data.
They use PTAT circuits and proprietary processing pipelines.
They need agents that can:

- Write tests that match team conventions
- Detect anomalies in sensor batch data
- Report findings in a structured, auditable format

### 5.1 The simulator

The sensor simulator provides:

- A synthetic PTAT data generator with injected anomalies
- Processing functions for agent test generation
- Pre-generated batch data for scenario runs

→ [adaptive-sensor-testing](https://github.com/adaptive-interfaces/adaptive-sensor-testing)

### 5.2 The three priors

### AO context (`ao-config-*.toml` + `ao-context-*.toml`)

How the team writes Python:
uv, src layout, hatchling, ruff, pyright strict,
Google docstrings, pytest, max 30 lines per function.

### ATD registry (`tools/sensor-api.toml`)

What the sensor data tool does:
inputs, outputs, failure modes, rate limits, constraints.

### Domain context (`ao-domain.toml`)

What correct means for PTAT sensor data:
expected frequency-temperature relationship,
operating ranges, anomaly definitions,
calibration check intervals.

### 5.3 Running the skill

```text
Read ACS
then read ATD registry
then read ao-config.toml and ao-config-python.toml
then read ao-domain.toml
then execute the task using the loaded priors and evaluation scenarios
```

### 5.4 Evaluating the output

- Test naming matches team conventions
- Fixtures used correctly
- Assertions match domain expectations
- Anomaly at sample 400 correctly identified
- Report structured per team standards

### 5.5 What surfaced

- Two conventions were inconsistent across repos
- The ATD registry revealed an undocumented failure mode
  in the sensor API
- The domain context forced a conversation about
  what "drift" means. The team had two definitions.

All three surfaced before the agent wrote a single line of test code.

## Part 6: The Broader Lesson

### 6.1 The discipline generalizes

The framework, including ACS, ATD, AO, and domain context,
applies to many domains, for example:

- Medical device validation
- Financial model testing
- Infrastructure automation
- Scientific data pipelines
- Any team where code review matters

The sensor testing example is a vehicle.
The discipline is the destination.

### 6.2 Priors improve the team independent of agents

Writing effective priors produces:

- Clearer onboarding documentation for new engineers
- More consistent code review feedback
- Explicit conversations about conventions
  that were previously implicit and inconsistent
- Documentation that reflects what the team actually does

The agent is the forcing function.
The benefit extends beyond the agent.

### 6.3 Writing skills is engineering

Writing a skill specification is an engineering task:

- It requires precision
- It requires testing (the evaluation scenarios)
- It requires maintenance (refresh when conventions change)
- It produces artifacts that can be reviewed, versioned, and forked

Teams that treat skill writing as engineering
produce agents that are reliable, auditable, and improvable.

Teams that treat it as prompt hacking
produce agents that work sometimes
and fail in ways no one can diagnose.

### 6.4 The lever

Team effectiveness with AI agents
is proportional to how precisely the team
has articulated how it works.

Writing that down is not overhead.
It is the work.

## Getting Started

### Step 1: Read ACS

Understand the foundational behavior protocol.
This is the foundation all adaptive-interfaces skills build on.

### Step 2: Run AO against one repo

Generate `ao-context.toml` for the most representative repo.
Review what the agent inferred.
Fill gaps by hand.
Have the conversation the priors surface.

### Step 3: Load and run

Start a session with ACS and AO loaded.
Give the agent a real task.
Evaluate the output against team conventions.

### Step 4: Iterate

Update priors where output missed.
Re-run.
Improvement should be immediate and traceable.

### Step 5: Extend

Add ATD for the tools.
Add domain context for the problem space.
Fork and tune the skills for specific team needs.

## Repository Organization

```text
adaptive-interfaces/
  adaptive-guide                      ← start here
  adaptive-conformance-specification  ← foundational behavior (ACS)
  adaptive-tool-discovery             ← map your tools (ATD)
  adaptive-onboarding                 ← capture your team (AO)
  adaptive-sensor-testing             ← complete worked example
  adaptive-skill-lab                  ← build and test your own skills
```

## License

MIT © 2026 [Adaptive Interfaces](https://github.com/adaptive-interfaces)
