# Agent Execution-Control Guardrails

English | [简体中文](execution-control-guardrails.md)

Purpose: copy the relevant sections into a system prompt, `AGENTS.md`, or task execution contract. These rules complement the general [long-session reliability guardrails](long-session-reliability-guardrails.en.md) and focus on baseline validation, retries, diagnostics, long-lived processes, and time budgets.

## 1. Minimum baseline

- Before automating a path, validate its core behavior with one command or a minimal script.
- For forwarding, proxying, or pipelines, first write down `source → bind → hop → target` and the success criterion for each segment.
- If the baseline fails, correct the direction, parameters, and prerequisites before adding relays, retries, or abstraction layers.
- For every new component, name the verified constraint it solves. If no such constraint can be stated, do not implement it yet.

## 2. Retry gate

- Before every retry, state what condition changed or what new evidence the attempt will produce.
- After two identical results with no progress, switch to diagnosis. Do not perform a fourth unchanged attempt.
- Retry automatically only for a specifically identified transient failure, with backoff, an attempt limit, and an overall deadline.
- Retries must not reset cumulative failure count or elapsed time.

## 3. Empty output and silent failure

- Empty output is neither proof of success nor sufficient reason to keep waiting.
- After empty output, inspect the exit code, stdout, stderr, exception traceback, process state, and elapsed time.
- Do not continue a loop through a broad exception handler that records no diagnostic information.
- A long-running task must expose at least one of: queryable logs, a heartbeat, or a progress metric.

## 4. Process lifecycle

- Before launching a command, classify it as a finite task, a long-lived process, or an event-driven task.
- Do not wait for a long-lived process as though it were expected to exit on its own.
- Retain a handle or PID for every long-lived process, and provide readiness detection, error collection, termination, and cleanup.
- `start_new_session` does not make an operation non-blocking. `Popen` does not remove the need for lifecycle management.

## 5. Time budget

- Calculate worst-case duration before execution: `attempts × per-attempt timeout + backoff + startup and cleanup overhead`.
- The internal worst-case duration must fit within the caller's deadline, with time reserved for diagnosis and cleanup.
- A timeout defines a failure action after elapsed time; it is not evidence that a background task started successfully.
- If actual duration diverges from the budget, stop extending waits and re-check the lifecycle model and blocking point.

## 6. Reporting

- Keep assistant turns, tool calls, and wall-clock time as separate units; do not combine them as “attempts.”
- Report the current hypothesis, minimum-baseline result, retry count, cumulative elapsed time, and missing evidence.
- If a solution is discarded because its foundational assumption was wrong, disclose the abandoned components and impact. Do not present added code volume as progress.
