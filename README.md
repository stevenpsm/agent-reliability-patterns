# Agent Reliability Patterns

English | [简体中文](README.zh-CN.md)

Field observations, failure patterns, practical guardrails, and reproducible evals for long-running LLM agents.

This repository collects recurring problems observed across models, agent harnesses, and long-horizon tasks, together with mitigation ideas that can be tested rather than merely asserted. It is not a model leaderboard. It asks engineering questions such as:

- Is a problem specific to one model and harness, or does it recur across systems?
- Does the failure originate in the model, the harness, project context, or task governance?
- Does a prompt rule measurably reduce failures, or only make the output look more disciplined?
- How should observations, root-cause hypotheses, injectable rules, and validation results be kept separate?

## Repository structure

```text
cases/       Sanitized timelines, command evidence, causal chains, and measurement units
patterns/    Model/harness observations with applicability and evidence boundaries
guardrails/  Short rules for system prompts, AGENTS.md files, or execution contracts
evals/       Repeatable scenarios, pass/fail criteria, and evaluation metrics
templates/   Templates for new field observations and pattern studies
```

Field cases, pattern analyses, and evaluation notes remain in Simplified Chinese because they preserve the language and context of the original work. The reusable guardrails are available in English and can be copied directly into an agent's runtime context.

## Ready-to-inject guardrails

- [Long-session agent reliability guardrails](guardrails/long-session-reliability-guardrails.en.md)
- [Agent execution-control guardrails](guardrails/execution-control-guardrails.en.md)

Each guardrail has a Chinese counterpart and a language switch at the top of the document.

## Current studies

The first study came from a long-running GLM-5.3 session through the pi coding agent:

- [GLM-5.3 + pi: six reliability failure patterns (Chinese)](patterns/glm-5.3-pi-long-session-failure-modes.md)
- [Long-session agent reliability guardrails (English)](guardrails/long-session-reliability-guardrails.en.md) · [Chinese](guardrails/long-session-reliability-guardrails.md)

The second study came from a Kimi K3 + pentest-agent session involving an execution-control failure chain:

- [Field case: SSH forwarding, repeated checks, and subprocess blocking (Chinese)](cases/2026-09-29-kimi-pentest-agent-execution-failures.md)
- [Pattern abstraction: evidence-free retries, complexity before baseline validation, and lifecycle mismatch (Chinese)](patterns/kimi-pentest-agent-long-session-execution-failures.md)
- [Execution-control guardrails (English)](guardrails/execution-control-guardrails.en.md) · [Chinese](guardrails/execution-control-guardrails.md)
- [Execution-control evaluation scenarios (Chinese)](evals/execution-control-scenarios.md)

## From observation to reusable knowledge

```text
Field case → Pattern candidate → Injectable guardrail → Controlled eval → Promotion or rejection
```

1. `cases/` records what happened, preserving chronology, evidence units, and causal dependencies.
2. `patterns/` asks which behavior may recur across tasks and defines inclusion and exclusion criteria.
3. `guardrails/` compresses mitigation ideas into short, executable, and checkable rules.
4. `evals/` fixes inputs, variables, metrics, and thresholds to compare runs with and without a guardrail.
5. Evidence status changes only after testing; a single field case does not establish a cross-model property.

Start a new observation with the [field-case template](templates/case-observation-template.md). Use the [pattern-study template](templates/pattern-study-template.md) only after a potentially generalizable behavior has been identified.

## Why the research documents are not named `AGENTS.md`

`AGENTS.md` is a runtime instruction entry point. It should not simultaneously carry a research report, root-cause hypotheses, and historical evidence. Injecting the full analysis into every session increases instruction competition and makes it harder to determine which rule actually changed behavior.

This repository separates those concerns:

1. `patterns/` preserves observations, evidence, hypotheses, and limitations.
2. `guardrails/` contains only rules that can be executed and checked.
3. Users select guardrails according to task risk and copy only the necessary rules into runtime context.
4. Effectiveness is measured with before/after metrics, not by asking a model whether it followed the rule.

## Evidence status

Every pattern document should use one of these states:

| Status | Meaning |
| --- | --- |
| `field-observation` | Observed in a real task but not reproduced under controlled conditions |
| `reproduced` | Repeated in the same model and harness under controlled conditions |
| `cross-model` | Independently reproduced across models or harnesses |
| `mitigated` | A mitigation passed a predefined comparative threshold |
| `not-reproduced` | A later experiment did not reproduce the behavior; retained as a negative result |

Status applies to a specific pattern, not automatically to every conclusion in the same document.

## Scope and limitations

- Model behavior may change with model versions, reasoning budgets, tool permissions, context compression, and harness implementation.
- A single long-running session can generate useful hypotheses, but cannot establish a property of all LLM agents.
- Root-cause descriptions remain hypotheses unless supported by implementation evidence or provider documentation.
- Guardrails have costs. Too many overlapping or conflicting rules may reduce task performance.
- This repository does not accept API keys, private code, customer data, real attack targets, or unsanitized transcripts.

## Contributing an observation

Use the [field-case template](templates/case-observation-template.md) to record the model, harness, task length, permissions, context strategy, chronology, evidence, and measurement units. Then use the [pattern-study template](templates/pattern-study-template.md) to define the candidate pattern, competing explanations, validation design, and promotion criteria.

Prefer submissions that a third party can reproduce, challenge, or falsify.
