# Goal Setter

Goal Setter is a Codex skill for turning ambiguous multi-step work into a reviewed, evidence-backed Goal file before any Goal is activated.

## Why This Exists

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

## Installation

Install the skill with Codex's skill installer from this repository:

```text
Use $skill-installer to install https://github.com/computerphilosopher/agent-skills/tree/main/skills/goal-setter
```

The skill installs to:

```text
${CODEX_HOME:-$HOME/.codex}/skills/goal-setter
```

Restart Codex after installation so the new skill is discovered.

## Usage

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

## Repository Layout

```text
skills/
└── goal-setter/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        └── goal-patterns.md
```
