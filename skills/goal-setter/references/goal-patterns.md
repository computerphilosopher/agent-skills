# Goal Patterns

## Core Summary

Use Goals for persistent objectives where the finish line is clear but the path is uncertain. The important shift is:

- Prompt: ask -> work -> result -> wait
- Goal: work -> check -> continue or complete

A strong Goal names the end state, proof, constraints, scope, iteration policy, and blocked stop condition. Evidence decides completion.

## Intent Interview Prompts

Use these prompts selectively. Ask the questions whose answers would most affect the Goal contract.

Core alignment:

```text
Before I turn this into a Goal, I want to pin down the contract so Codex does not spend time optimizing the wrong thing:
1. What exact end state should count as done?
2. What should Codex avoid changing or pursuing?
3. What evidence should decide success?
```

Verification:

```text
Which concrete check should be authoritative: local tests, CI, a benchmark threshold, a generated artifact, manual review, source citations, or something else?
```

Scope:

```text
What scope is allowed: specific files/modules only, the whole repo, external research, network access, dependency changes, migrations, or generated artifacts?
```

Autonomy and risk:

```text
How much autonomy should the Goal allow: keep iterating through likely fixes, make only low-risk changes, stop before edits, or ask before expensive/destructive steps?
```

Budget:

```text
What limit should stop the Goal: time, token budget, number of iterations, command/runtime cost, or "stop when no defensible path remains"?
```

Blocked condition:

```text
If the evidence is unavailable or the target looks unreachable, what should Codex report, and what input would unlock progress?
```

Confirmation before activation:

```text
I understand the Goal as: <outcome>. Success is proven by <evidence>. Codex must preserve <constraints>, stay within <scope>, and stop if <blocked condition or budget>. Non-goals are <non-goals>. Confirm this contract before I save it to goals/<goal-name>.md.
```

## Markdown Goal File

Save Goal specs at `project-root/goals/{goal-name}.md`. Use the project Git root when available; otherwise use the current working directory or ask the user which root should own the Goal.

A Goal file is not active Goal state. It is a reviewable activation artifact; the Goal becomes active in the current thread only when the user runs `/goal @goals/<goal-name>.md`.

Use this structure:

```markdown
# Goal: <Human-readable title>

Slug: `<goal-name>`
Status: Draft
Activation: `/goal @goals/<goal-name>.md`

This file is not an active Goal. It becomes active thread-scoped Goal state only after the activation command is run.

## Goal Text

<A single strong Goal statement. Do not include the literal "/goal" prefix here unless the user wants it.>

## Interview Summary

- Desired outcome: <what done means>
- Non-goals: <what Codex should avoid>
- Evidence: <tests, benchmarks, artifacts, logs, sources, or review checks>
- Constraints: <things that must not regress>
- Scope: <allowed repos, files, tools, data, network, credentials>
- Autonomy: <how aggressively Codex may iterate>
- Budget: <time, token, iteration, command, or cost limits>
- Blocked condition: <when to stop and ask>
- Reporting: <expected progress/final format>

## Assumptions

- <assumption made because the user did not specify it>

## Verification Checklist

- [ ] <evidence check>
- [ ] <constraint check>

## Notes

<Optional context, links, or prompt history.>
```

When reporting back, say:

```text
Saved the Goal spec to goals/<goal-name>.md. Review it, then run:
/goal @goals/<goal-name>.md
```

## Weak to Strong

Weak:

```text
/goal Improve performance
```

Strong:

```text
/goal Reduce p95 checkout latency below 120 ms, verified by the checkout benchmark, while keeping the correctness suite green. Use only the checkout service, benchmark fixtures, and related tests. Between iterations, record what changed, what the benchmark showed, and the next best experiment to try. If the benchmark cannot run or no valid paths remain, stop with the attempted paths, evidence gathered, blocker, and next input needed.
```

Weak:

```text
/goal Write docs for this feature
```

Strong:

```text
/goal Produce a docs page for this feature that explains the lifecycle, command surface, and two realistic examples, verified by the local docs build and by checking that referenced commands match current CLI behavior. Preserve existing docs style and avoid changing unrelated pages. If the build or command verification is blocked, stop with the failing command, evidence, and the next input needed.
```

Weak:

```text
/goal Reproduce this paper
```

Strong:

```text
/goal Produce the strongest evidence-backed reproduction of the paper using available materials and local resources. Build a claim inventory, attempt the headline results where feasible, verify outputs where possible, and end with a report separating confirmed findings, approximate reconstructions, blocked exact replay, and remaining uncertainty. If source data, seeds, checkpoints, or methods are unavailable, label those claims as blocked instead of overstating success.
```

## Templates

Performance tuning:

```text
/goal Reduce <metric> below <threshold>, verified by <benchmark command or report>, while keeping <correctness tests or behavior constraints> green. Use <service/modules/fixtures> only. Between iterations, compare measurements before and after the change and choose the next smallest defensible experiment. If measurement cannot run or no valid paths remain, stop with attempted changes, evidence, blocker, and next input needed.
```

Flaky test investigation:

```text
/goal Make <test name or suite> pass reliably on the current branch, verified by <repeat command/count or CI check>, while preserving public API behavior and unrelated tests. First reproduce the failure, then isolate cause before changing code. Between iterations, record reproduction evidence, hypothesis, change, and result. If the failure cannot be reproduced or the cause cannot be isolated under current limits, stop with evidence, attempted paths, and what would unlock progress.
```

Migration:

```text
/goal Complete <migration target>, verified by <build/test/lint/runtime checks>, while preserving <public behavior, data compatibility, or API contract>. Limit changes to <scope>. Between iterations, fix the next highest-confidence incompatibility shown by evidence. If a dependency, API gap, or manual decision blocks completion, stop with the blocker, affected files, evidence, and decision needed.
```

Research audit:

```text
/goal Produce an evidence-backed audit of <research question/source>, verified by a final report with claim-by-claim evidence. Use <allowed sources/resources>. Separate confirmed claims, proxy support, contradictions, blocked claims, and remaining uncertainty. Between iterations, map each claim to the strongest available evidence before adding conclusions. If source material is unavailable or evidence is insufficient, label the claim blocked or uncertain and explain what evidence would resolve it.
```

Generated artifact:

```text
/goal Produce <artifact>, verified by <render/build/check command and manual/visual inspection criteria>, while preserving <style, compatibility, or content constraints>. Use <templates/assets/source material>. Between iterations, inspect the artifact, fix the highest-impact defect, and rerun verification. If the artifact cannot be generated or verified, stop with logs, partial output path, blocker, and next input needed.
```

## Completion Language

When reporting progress under a Goal, keep status evidence-based:

- Complete: objective was verified against the named evidence.
- Improved but incomplete: evidence moved in the right direction but missed the target or violated a constraint.
- Blocked: a required command, artifact, source, credential, data set, or decision is unavailable.
- Budget-limited: work stopped due to budget; this is not completion.

For research, preserve levels of support:

```text
Claim: <claim>
Route: <how it was tested or reconstructed>
Evidence surface: <files, commands, figures, outputs, sources>
Status: <confirmed | approximate support | contradicted | blocked | uncertain>
Remaining uncertainty: <what is still unknown>
```
