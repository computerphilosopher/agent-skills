# Agent Skills

Reusable skills for coding agents.

| Skill | Purpose |
| --- | --- |
| [코드검증](skills/code-verification/SKILL.md) | Verify code changes against specifications, review implementation and tests, and run applicable browser and repository checks. |
| [문서 길잡이](skills/document-walkthrough/SKILL.md) | Guide document reading, explain one step at a time, answer questions, and resume from the current reading position. |
| [문장강화](skills/munjang-ganghwa/SKILL.md) | Revise documents through content pruning, structure review, and sentence refinement. |
| [Document Thanos](skills/document-thanos/SKILL.md) | Delete unnecessary document content and consolidate redundant explanations while preserving needed information. |
| [Document Structure Review](skills/document-structure-review/SKILL.md) | Improve document organization, explanation coverage, tables, terminology consistency, and procedural flow. |
| [Sentence Expression Review](skills/sentence-expression-review/SKILL.md) | Refine sentence wording, awkward noun lists, and unclear references. |
| [False Green Detector](skills/false-green-detector/SKILL.md) | Find tests that pass even when behavior violates the specification. |
| [Session Handoff](skills/session-handoff/SKILL.md) | Transfer a task to a new Codex chat and separate Git worktree, then archive the original chat after verified intake. |
| [Goal Setter](skills/goal-setter/SKILL.md) | Turn ambiguous multi-step work into a reviewed, evidence-backed Goal file. |
| [UI Simplify](skills/ui-simplify/SKILL.md) | Review a screen and fix unnecessary text, redundant buttons, and environment-specific UI by default. |

## 코드검증 (Code Verification)

코드검증 runs the following skills in order — `spec-to-code-compliance`, `ponytail-review`, `false-green-detector`, `impeccable`, `webapp-testing`, and `verification-before-completion`. It compares the code with its specification, reviews the implementation and tests, uses [Impeccable](https://github.com/pbakaus/impeccable) only for changes affecting web, desktop, or mobile UI, checks local web interactions where applicable, then runs repository checks against the final state. In `fix` mode, it selects an Impeccable editing command for the affected screen or component. Each applicable stage reads the installed skill's instructions. Stages outside the requested scope are reported as not applicable; applicable stages that cannot run are reported as unreviewed.

The specification stage uses the [Trail of Bits plugin](https://github.com/trailofbits/skills/tree/main/plugins/spec-to-code-compliance), including its workflow and checker agent. When the host lacks the workflow runner, the orchestrator maps those roles to independent subagents and preserves the original evidence and refutation requirements. The browser stage uses [Anthropic's webapp-testing skill](https://github.com/anthropics/skills/tree/main/skills/webapp-testing) and requires Python Playwright and a browser in the test environment.

The skill's automatic selection conditions cover substantial feature implementation before completion, verifying the approved feature scope and its affected callers, tests, and user flows. This path uses `fix` mode for problems within that implementation scope. Small wording, formatting, documentation-only, and low-impact changes are excluded. Implicit invocation is enabled; selection depends on the host matching the skill description. Explicit verification defaults to preserving source, tests, and configuration; `review` always preserves them. Use `fix` to request corrections explicitly.

When the user requests Goal-based verification, including a standing request to run it after substantial features, the skill reuses the current task's active Goal or creates a verification Goal when no unfinished Goal exists. Each resumed turn reads the work record and continues pending items. Review Goals finish when all requested checks and evidence are complete; fix Goals also require scoped corrections and rechecks. An existing implementation Goal retains its original completion criteria. Ordinary implementation or review requests alone do not authorize a new Goal. If native Goal tools are unavailable, the skill supplies a concrete `/goal` command and continues available verification. This workflow adds no external runner or script.

Before execution, the skill inventories the requested requirements, code paths, tests, and user flows. It completes each applicable stage before starting the next; partial results from a worker's item or budget limit feed the remaining work queue. It continues while actionable items remain and reports each stage with coverage and evidence. Only an actual dependency block or a user-defined overall budget or stop request permits an incomplete final report. Passing automated checks alone does not complete the workflow.

Verify the current implementation changes, or name specific files, commits, or a branch:

```text
$code-verification
$code-verification review path/to/code
```

Fix confirmed problems and verify again:

```text
$code-verification fix path/to/code
```

## 문서 길잡이 (Document Walkthrough)

Document Walkthrough helps readers choose where to start, understand a document one step at a time, and continue after questions without losing their place. It connects explanations to source locations, distinguishes documented facts from inference, and checks the current document when resuming. Explanation is the default; reading guidance alone does not edit the source.

Install the walkthrough and its required editing skills together:

```text
Use $skill-installer to install these paths from computerphilosopher/agent-skills:
- skills/document-walkthrough
- skills/munjang-ganghwa
- skills/document-thanos
- skills/document-structure-review
- skills/sentence-expression-review
```

Choose a reading path or begin a guided explanation:

```text
$document-walkthrough path/to/document.md 어디부터 읽으면 좋을지 안내해줘.
$document-walkthrough path/to/document.md 호출 예부터 한 단계씩 설명해줘.
```

Continue with questions such as “이 객체는 무슨 역할이야?”, “그다음 설명해줘”, or “어디까지 봤지?” in the same chat.

Edits requested during a walkthrough go to a separate editing chat for each document while reading continues. The guide reuses a chat only for the same document, tracking its full path or document ID, editing session, and repository and original branch where applicable. After the user authorizes document-specific chats, messages in both directions, and automatic merges for Git documents, independent edits, Munjang Ganghwa, and checks run in parallel. Shared files and dependencies are coordinated. Each Git document has its own worktree and branch; the guide schedules merges into each original checkout one at a time.

Before reporting completion or closing a session, the editing chat must apply Munjang Ganghwa in revision mode to the approved scope and verify the final document. For Git documents, it commits the changes and merges them into the original branch without repeated confirmation; other documents are saved to their source. It reports the document, request, merge or save result, and Munjang Ganghwa results. The guide checks the updated source without switching documents or advancing the reader's place. Users can still request edits in the current chat.

Before creating an editing chat, use the user's chosen model and reasoning effort. If no choice is recorded, suggest GPT-6 Luna / Max (`model: "gpt-6-luna"`, `thinking: "max"`) and wait for confirmation. Do not ask again once the user has chosen, silently substitute an unsupported combination, or change either chat's settings on follow-up messages without a user request.

## 문장강화 (Munjang Ganghwa)

문장강화 is a comprehensive document revision workflow, named as an homage to Yi Tae-jun’s *Munjang Ganghwa*. It applies Document Thanos, Document Structure Review, and Sentence Expression Review in that order. Revision is the default; each stage edits the previous stage’s revised document. Use `review` or explicitly request a review without edits to receive findings only. The final report summarizes what changed and why. Questions requiring a new policy or design decision remain for the user.

Install the orchestrator and its three supporting skills together:

```text
Use $skill-installer to install these paths from computerphilosopher/agent-skills:
- skills/munjang-ganghwa
- skills/document-thanos
- skills/document-structure-review
- skills/sentence-expression-review
```

Revise a document (default):

```text
$munjang-ganghwa path/to/document.md
```

Review without editing:

```text
$munjang-ganghwa review path/to/document.md
```

## Document Thanos

Document Thanos removes unnecessary sentences, paragraphs, tables, examples, and sections. It checks what information readers would lose if each explanation were removed, consolidates duplicate definitions, and preserves necessary conditions, exceptions, background, and source material. Editing is the default; an explicit review-only request returns deletion candidates without changing files. It has no deletion quota.

Use Document Thanos for document pruning, Document Structure Review for organization and explanation coverage, and Sentence Expression Review for sentence clarity.

Install from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/document-thanos
```

Use it to prune a document:

```text
Use $document-thanos to remove unnecessary content from this document while preserving information its readers need.
```

## Document Structure Review

Document Structure Review checks whether the required definitions, conditions, relationships, and procedures are explained and connected. It covers explanation completeness, section hierarchy, information placement, table organization, and term definitions. It applies supported improvements by default; an explicit review-only request returns findings without changing files.

Install from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/document-structure-review
```

Use it to improve a document:

```text
Use $document-structure-review to improve this document's explanation coverage, section hierarchy, and table organization.
```

## Sentence Expression Review

Sentence Expression Review clarifies relationships hidden by noun lists, ambiguous wording, and unclear references. It edits by default while preserving the original meaning; an explicit review-only request returns findings without changing files.

Install from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/sentence-expression-review
```

Use it to refine wording:

```text
Use $sentence-expression-review to refine the wording in this document.
```

The [original document review skill](skills/sentence-expression-review/tests/fixtures/document-readability-original.md) is preserved unchanged as a test fixture for independent reviews.

## False Green Detector

False Green Detector reviews tests against specifications. It checks where expected values come from, which outcomes assertions actually verify, and whether incorrect implementations can still pass. It distinguishes test gaps from implementation defects and unclear requirements.

Install from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/false-green-detector
```

Use it to review existing tests:

```text
Use $false-green-detector to review existing tests against the specification and find incorrect behavior they would fail to detect.
```

## Session Handoff

Invoking Session Handoff creates a new Codex chat, prepares a separate worktree for repository tasks, transfers the task context and materials, verifies intake, and then archives the original chat. The handoff includes unresolved decisions, unfinished work, and verified Git state, referring to repository instructions instead of repeating them. Only an explicit request for a handoff prompt alone produces text without creating a chat or worktree or archiving the original chat. Requests to explain, review, or edit the skill do not execute a handoff.

Install from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/session-handoff
```

Use it to transfer the task and archive this chat after verified intake:

```text
$session-handoff
```

Request only a handoff prompt:

```text
Use $session-handoff to write only a handoff prompt for continuing this task in another session.
```

## UI Simplify

Install from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/ui-simplify
```

Review and update a screen:

```text
Use $ui-simplify on this screen.
```

## Goal Setter

Goal Setter is a Codex skill for turning ambiguous multi-step work into a reviewed, evidence-backed Goal file before any Goal is activated.

### Why This Exists

Codex Goals are powerful because they let a thread keep working toward a persistent objective across turns. That same persistence can waste a lot of time and tokens if the Goal is even slightly misaligned with the user's intent.

Goal Setter reduces that risk by separating Goal design from Goal activation. It guides Codex through an intent-alignment interview, records the agreed contract in markdown, and leaves activation as an explicit user action.

Instead of immediately running a Goal, it saves a spec like:

```text
goals/<goal-name>.md
```

You can review that file, keep it in version control, and start the Goal later with:

```text
/goal @goals/<goal-name>.md
```

The markdown file is not an active Goal by itself. It is a reviewable activation artifact; the Goal becomes active only after you run `/goal @goals/<goal-name>.md` in a Codex thread.

### Installation

Install the skill with Codex's skill installer from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/goal-setter
```

The skill installs to:

```text
${CODEX_HOME:-$HOME/.codex}/skills/goal-setter
```

Restart Codex after installation so the new skill is discovered.

### Usage

Invoke the skill when you want to turn a task into a durable Codex Goal:

```text
Use $goal-setter to create a Goal for reducing checkout p95 latency below 120 ms without regressing correctness tests.
```

Goal Setter will interview for:

- the exact desired outcome,
- non-goals and boundaries,
- evidence that proves success,
- constraints that must not regress,
- autonomy and risk tolerance,
- budget or stopping limits,
- blocked conditions and reporting format.

After the contract is clear, it writes a markdown file under the current project root:

```text
goals/<goal-name>.md
```

Then you can review and activate it manually:

```text
/goal @goals/<goal-name>.md
```

Claude also has a Goal-like feature, so this workflow may be adaptable there, but it has not been tested with Claude yet.

### Repository Layout

```text
skills/
├── code-verification/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── munjang-ganghwa/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── document-thanos/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── document-structure-review/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── document-walkthrough/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── false-green-detector/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── goal-setter/
│   ├── SKILL.md
│   ├── agents/
│   │   └── openai.yaml
│   └── references/
│       └── goal-patterns.md
├── sentence-expression-review/
│   ├── SKILL.md
│   ├── agents/
│   │   └── openai.yaml
│   └── tests/
│       └── fixtures/
│           └── document-readability-original.md
├── session-handoff/
│   └── SKILL.md
└── ui-simplify/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```
