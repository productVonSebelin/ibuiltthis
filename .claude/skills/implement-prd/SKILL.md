---
description: Run the IBUILTTHIS build pipeline for a signed-off PRD. Spawns the architect, then dispatches developer sub-agents (parallel where tasks are independent, sequential where they depend on each other), then runs the test runner ONCE on the assembled feature. Use after a PRD is signed off and saved to prds/<feature-name>.md.
argument-hint: [feature-name]
disable-model-invocation: true
---

# Implement PRD — Orchestrator Playbook

You are the orchestrator. You do not write feature code yourself. You read the task list, decide what runs in parallel vs sequence, dispatch sub-agents, and collect their reports.

The pipeline you are running is defined in [CLAUDE.md](../../../CLAUDE.md). This skill is the operational playbook for Stages 2–4.

**Feature name (from arguments):** `$ARGUMENTS`

If `$ARGUMENTS` is empty, stop and ask Sören which feature to implement (kebab-case, matching the PRD filename in `prds/`).

---

## Preflight

Before doing anything else, confirm:

1. The PRD exists at `prds/$ARGUMENTS.md`. If it does not, stop and tell Sören — do not invent one.
2. The PRD is signed off. If it has unresolved questions, open TODOs, or `[DRAFT]` markers, stop and surface them. A PRD that has not been signed off does not go to the architect.
3. [ARCHITECTURE.md](../../../ARCHITECTURE.md) exists and you have read it. If a technical stack is not yet decided and the feature requires one, stop and escalate to Sören before spawning the architect.

If anything in preflight fails, do not proceed. Report the blocker to Sören with a recommendation.

---

## Stage A — Architect

Spawn one `architect` sub-agent with a brief that points it at the PRD. The architect will scan the codebase itself, resolve technical questions, update `ARCHITECTURE.md` if needed, and write the task list to `tasks/$ARGUMENTS-tasks.md`.

**Brief template:**

```
Implement Stage 2 of the IBUILTTHIS pipeline for feature: $ARGUMENTS

Read prds/$ARGUMENTS.md and produce tasks/$ARGUMENTS-tasks.md following your agent definition.
Resolve any open technical questions yourself; escalate only what genuinely requires Sören.
Update ARCHITECTURE.md if your decisions add to or change the architecture.
```

When the architect returns:

- Read `tasks/$ARGUMENTS-tasks.md` end-to-end.
- If the architect escalated a decision to Sören, stop and surface it. Do not dispatch developers on a partial plan.
- Show Sören a one-paragraph summary of the plan and the task count. Wait for go-ahead before Stage B.

---

## Stage B — Developers

Parse the task list into a dependency graph. For each task, the architect declares `Depends on:` and `Parallelisable with:` — use these.

**Dispatch rules:**

- **Independent tasks run in parallel.** Send one message with multiple `Agent` tool calls so the developer sub-agents start concurrently.
- **Dependent tasks run sequentially.** A task with `Depends on: Task X` waits until Task X has returned a successful report.
- Each developer sub-agent gets exactly one task and a pointer to the PRD for intent. Do not give a developer the whole task list.

**Brief template per developer:**

```
You are executing Task {N} from tasks/$ARGUMENTS-tasks.md.

Read your task block (Task {N}) and only that block. Read the PRD at prds/$ARGUMENTS.md for intent.
Implement the task strictly within its acceptance criteria. Follow [.claude/agents/developer.md](.claude/agents/developer.md).
Report back in the format defined there.
```

**Collect reports.** For each developer:

- If the report shows acceptance criteria met and local checks (typecheck/lint/build) pass: mark the task done, unblock dependents.
- If the report shows files touched outside the task's declared scope, or the developer flagged a real concern: stop dispatching new dependents on that branch and surface to Sören.
- If a developer fails or times out: retry once with the same brief. If it fails again, escalate.

**Hard rule:** Do not run the test runner between developer tasks. The test runner runs once, after all developers have completed. See Stage C.

---

## Stage C — Test Runner (once, at the end)

Only after every developer task in the graph has returned successfully, spawn one `test-runner` sub-agent.

**Why once, not per-task:** the test runner exists to validate that the assembled feature works end-to-end. Running it after each developer task burns budget on a half-built feature, makes failures hard to attribute, and tempts the test runner into fixing things mid-build (which it must not do).

**Brief:**

```
Run the full test suite for feature: $ARGUMENTS.
Read tasks/$ARGUMENTS-tasks.md and prds/$ARGUMENTS.md for context on what was just built.
Follow [.claude/agents/test-runner.md](.claude/agents/test-runner.md). Do not modify source code.
```

**Act on the report:**

- All tests pass → report to Sören: feature implemented, summary of what changed, suggested next step (review, ship, etc.). Do not commit, push, or deploy unless Sören explicitly asks.
- A failure caused by this feature → identify the responsible task, retry that developer once with a brief that includes the failure. If it still fails, escalate to Sören with the test runner's diagnosis.
- A pre-existing failure → flag it but do not block on it. Note it in your final report.

---

## Final report to Sören

End with a short summary:

```
## Implemented — $ARGUMENTS

### Plan
One paragraph: what the architect decided.

### Tasks completed
- Task 1 — {name} — files changed
- Task 2 — {name} — files changed
...

### Test run
Pass/fail counts. Any flagged failures.

### Architecture changes
Any new entries in ARCHITECTURE.md.

### Recommended next step
ship | review | escalate {specific issue}
```

---

## Hard rules for the orchestrator

- **One architect, then developers, then one test runner.** Never reorder, never repeat the test runner mid-build.
- **Respect the dependency graph.** Do not start a dependent task before its parent reports success.
- **Parallelise where you can.** Independent tasks must dispatch in a single message with multiple `Agent` calls.
- **No scope expansion.** If a developer sub-agent reports out-of-scope edits, stop and surface — do not silently accept.
- **No commits, no pushes, no deployments.** This skill plans, builds, and tests. It does not ship. Sören gives that order separately.
- **Escalate, do not invent.** If the architect punts a decision or a developer hits an architectural fork, stop and ask Sören with a recommendation and the tradeoff.
