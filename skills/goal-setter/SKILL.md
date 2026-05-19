---
name: goal-setter
description: Draft, critique, strengthen, and save Codex Goal specs as markdown files using a careful intent-alignment interview and evidence-backed completion criteria. Use when the user asks to write a /goal, improve a weak Goal, decide whether a Goal or normal prompt fits, prepare a Goal file for /goal @file usage, or turn debugging, benchmarking, flaky-test, migration, research, documentation, or multi-turn investigation work into a persistent objective with verification, constraints, boundaries, iteration policy, and blocked stop conditions.
---

# Goal Setter

## Overview

Help the user turn uncertain multi-step work into a strong Codex Goal spec. A Goal is a thread-scoped completion contract: define the outcome, verify it with evidence, preserve constraints, and continue only until the evidence says the work is complete or honestly blocked.

Default to interviewing before drafting or saving a Goal. Small mismatches can waste substantial time and tokens, so align on intent before encoding the objective.

Do not activate Goals directly by default. Save the agreed Goal as a markdown file under `project-root/goals/{goal-name}.md` so the user can review it, preserve prompt history, and invoke it later with `/goal @goals/{goal-name}.md`.

A saved Goal file is not an active Goal. It is a reviewable activation artifact; the Goal becomes active thread-scoped state only after the user runs `/goal @goals/{goal-name}.md`.

## Decision Rules

Recommend a Goal when the task has:

- a durable objective that may take several turns,
- an evidence-based finish line such as tests, benchmarks, logs, source material, or a final artifact,
- an uncertain path where the next step depends on what Codex learns.

Prefer a normal prompt for one-line edits, simple explanations, short code reviews, and questions where the user wants one answer and then a stop.

Reject vague finish lines. Do not leave goals as "improve this", "make it better", or "refactor the code" unless the expected end state, verification surface, and constraints are clear.

## Intent Alignment Interview

Interview first unless the user already supplied all completion-critical details. Keep questions concise, but be thorough enough to prevent distorted work.

Before drafting, establish:

- Desired outcome: What should be true when the Goal is complete?
- Non-goals: What should Codex avoid doing, even if it seems useful?
- Evidence: Which tests, benchmarks, logs, artifacts, sources, or review checks prove success?
- Constraints: Which behavior, APIs, performance, style, data, files, or compatibility must not regress?
- Scope: Which repositories, directories, files, tools, networks, credentials, or data sources are allowed?
- Autonomy: Should Codex keep iterating aggressively, make only low-risk changes, or stop before edits?
- Budget: What time, token, iteration, command, or cost limit should stop work?
- Blockers: When should Codex stop and ask instead of continuing?
- Reporting: What progress format or final artifact does the user expect?

Ask the highest-risk missing questions first. Prefer 2-5 targeted questions over a long questionnaire. Continue the interview if the answers reveal new ambiguity.

After interviewing, restate the interpreted contract in plain language and ask for confirmation before writing or updating the Goal file. Include assumptions in the file.

## Goal File Workflow

Use file-based Goals as the default handoff.

1. Identify the project root. Prefer the Git root when available; otherwise use the current working directory or ask if multiple roots are plausible.
2. Create `goals/` under the project root if needed.
3. Derive `{goal-name}` from the intended outcome using lowercase kebab-case.
4. Write the Goal spec to `goals/{goal-name}.md`.
5. Include the exact activation command in the file and final response: `/goal @goals/{goal-name}.md`.
6. State that the file itself does not activate the Goal; it only becomes active after the user invokes it with `/goal @...`.
7. If the file already exists, update it intentionally rather than creating a duplicate, unless the new request is clearly a different Goal.

The markdown file should preserve the prompt contract, not just the final `/goal` sentence. Include:

- title and short slug,
- activation command,
- final Goal text,
- interview summary,
- assumptions,
- non-goals,
- verification evidence,
- constraints and boundaries,
- iteration policy,
- blocked stop condition,
- budget or stopping limits,
- expected final report format.

## Drafting Workflow

1. Interview for intent alignment and missing completion-critical details.
2. Restate the user's intent, non-goals, success evidence, constraints, and stop conditions.
3. Identify the desired end state.
4. Name the verification surface: command, test, benchmark, artifact, report, log, or source material.
5. Add constraints that must stay true while working.
6. Add boundaries around files, tools, data, repositories, or resources if scope matters.
7. Add an iteration policy for choosing the next action after each attempt.
8. Add a blocked stop condition describing when to stop and what to report.
9. Write or update the markdown Goal file.
10. Report the file path and activation command.

Use this pattern:

```text
/goal <desired end state>, verified by <specific evidence>, while preserving <constraints>. Use <allowed inputs, tools, or boundaries>. Between iterations, <how to choose and record the next best action>. If blocked or no valid paths remain, stop with <attempted paths, evidence, blocker, and next input needed>.
```

Ask follow-ups when a missing detail could make the Goal unverifiable, unsafe, too broad, too costly, or likely to optimize for the wrong outcome. Otherwise, make conservative assumptions and state them briefly.

## Goal Lifecycle

If the user asks to draft, improve, set, or prepare a Goal, write or update the markdown Goal file.

Do not create or activate the Goal directly as part of this skill. Leave the activation as an explicit user action with `/goal @goals/{goal-name}.md`, unless the user gives a separate instruction after reviewing the file.

If the user asks to view, pause, resume, clear, or otherwise manage a Goal, use the available Goal command or tool surface. Do not invent lifecycle state.

Mark a Goal complete only when the objective has been checked against concrete evidence. Budget limits, plausible progress, or a good-looking artifact are not completion.

## Completion Audit

Before calling a Goal strong enough, check:

- Outcome: What should be true at the end?
- Evidence: What proves it?
- Constraints: What must not regress?
- Boundaries: What scope is allowed?
- Iteration: What should Codex do after each attempt?
- Blocked state: What should Codex report if the work cannot be completed under current limits?
- Intent fidelity: Did the user confirm the interpretation, including non-goals and stop conditions?

If the Goal is for research, require the final artifact to separate confirmed findings, approximate or proxy support, blocked claims, and remaining uncertainty.

## References

Read `references/goal-patterns.md` when you need examples, interview prompts, the markdown Goal file template, weak-to-strong rewrites, or domain-specific templates for performance, flaky tests, documentation, migrations, and research audits.
