---
name: dev-loop
description: "Use when starting medium-to-large feature development that requires multiple iterations of prototyping, testing, and refinement. Orchestrates a continuous development loop: discuss → prototype → verify → debug → repeat until deliverable."
---

# Development Loop

## Overview

Orchestrate iterative feature development through a structured loop: **Discuss → Prototype → Verify → Debug → Repeat**. Each iteration builds on the previous round's findings until the feature is deliverable.

This skill coordinates existing skills into a continuous development cycle. It is the **master orchestrator** — individual phases delegate to specialized skills.

**Announce at start:** "Using dev-loop to orchestrate iterative development for [feature name]."

## Flow

```
/dev-loop start
    │
    ▼
┌─────────────┐
│  ① Discuss   │◄──────────────────────┐
│  (choose     │                        │
│   mode)      │                        │
└──────┬──────┘                        │
       ▼                               │
┌─────────────┐                        │
│  ② Prototype │                        │
│  (write code)│                        │
└──────┬──────┘                        │
       ▼                               │
┌─────────────┐                        │
│  ③ Verify    │                        │
│  (checklist  │                        │
│   + tests)   │                        │
└──────┬──────┘                        │
       │                               │
   Pass? ──Yes──► Ask user: Deliverable?│
       │              │    │           │
       No             No   Yes         │
       │              │    │           │
       ▼              │    ▼           │
┌─────────────┐       │  End loop     │
│  ④ Debug     │       │  (summary)   │
│  + report    │       │              │
└──────┬──────┘       │              │
       └──────────────┴──────────────┘
```

## State Tracking

Use TaskCreate/TaskUpdate to track loop state throughout the process:

- **Main task:** `dev-loop: [feature name]` — tracks current phase and round number
- **Phase tasks:** One task per active phase, updated as work progresses
- **Round counter:** Increment on each return to Phase ①

At the start of each round, update the main task with:
```
Round N — Phase: [current phase]
Previous findings: [summary of last round's debug report / verification results]
```

---

## Phase ① — Discuss

### First Round

Use AskUserQuestion to present discussion mode options:

```
Starting dev-loop Round 1 for [feature name].

How would you like to discuss requirements?

A. **Claude leads** — I'll ask questions one at a time about requirements, constraints, and technical choices
B. **You describe, I organize** — Share your thoughts freely, I'll structure them and confirm
C. **Brainstorm together** — We'll explore ideas collaboratively (uses brainstorming skill)

Pick A, B, or C:
```

**Mode A — Claude leads:**
- Ask questions one at a time (never batch)
- Cover: purpose, target users, constraints, success criteria, technical preferences
- Prefer multiple-choice when possible

**Mode B — User describes:**
- Let user share freely
- Organize into structured format
- Confirm each section with user

**Mode C — Brainstorm:**
- Invoke the `brainstorming` skill
- Let it run its full process

### Subsequent Rounds (Round 2+)

Bring forward context from previous rounds:

```
Starting dev-loop Round N for [feature name].

From last round:
- Verification results: [pass/fail summary]
- Debug findings: [root cause and fix summary from debug report]
- Open issues: [remaining items]

What should we focus on this round?
A. Address the issues found — refine the approach based on debug findings
B. Expand scope — add new requirements or change direction
C. Brainstorm alternatives — the current approach needs rethinking
```

### Phase ① Output

Produce a structured requirements document (can be inline or saved to file):

```markdown
## Feature: [name]
### Goal
[1-2 sentence objective]
### Technical Approach
[chosen approach with key decisions]
### Acceptance Criteria
- [ ] [criterion 1]
- [ ] [criterion 2]
- [ ] ...
### Constraints
[any technical or business constraints]
### Out of Scope
[explicitly excluded items]
```

Confirm with user before proceeding to Phase ②.

---

## Phase ② — Prototype

### Step 1: Plan

Invoke the `writing-plans` skill to produce an implementation plan based on the Phase ① requirements document.

- Save plan to `docs/plans/YYYY-MM-DD-[feature-name].md`
- If a plan already exists from a prior round, update it rather than creating a new one

### Step 2: Execute

Invoke the `executing-plans` skill to implement the plan.

- Write the minimum viable prototype that satisfies the acceptance criteria
- If TDD is appropriate for the feature, integrate the `test-driven-development` skill
- Commit frequently with descriptive messages

### Step 3: Checkpoint

After execution completes, briefly summarize what was built before moving to Phase ③.

---

## Phase ③ — Verify

### Step 1: Build Verification Checklist

Generate a checklist from the Phase ① acceptance criteria:

```
Verification Checklist for [feature name] — Round N

From acceptance criteria:
- [ ] [criterion 1] — [how to verify]
- [ ] [criterion 2] — [how to verify]
- [ ] ...

Technical checks:
- [ ] Code compiles / no syntax errors
- [ ] Tests pass
- [ ] No regressions in existing functionality
```

### Step 2: Execute Verification

Invoke the `verification-before-completion` skill.

- Run all automated checks (tests, linting, type checking)
- For items requiring manual confirmation, ask user with AskUserQuestion
- Record pass/fail for each checklist item

### Step 3: Produce Verification Report

```markdown
## Verification Report — Round N

| # | Criterion | Status | Evidence |
|---|-----------|--------|----------|
| 1 | [criterion] | PASS/FAIL | [command output or user confirmation] |
| 2 | [criterion] | PASS/FAIL | [evidence] |

**Result: [ALL PASS / X of Y passed]**
```

### Step 4: Branch Decision

- **All items pass** → Ask user: "All verification checks passed. Is this feature ready to deliver?"
  - **Yes** → Go to **Loop Exit**
  - **No** → Return to **Phase ①** (Round N+1) with verification report as context
- **Any item fails** → Proceed to **Phase ④**

---

## Phase ④ — Debug + Report

### Step 1: Debug

Invoke the `systematic-debugging` skill for each failing verification item.

- Follow the skill's full process (reproduce → hypothesize → investigate → root cause → fix)
- Do NOT skip root cause analysis

### Step 2: Generate Debug Report

Save a debug report to: `docs/debug-reports/[feature-name]-round[N]-YYYY-MM-DD.md`

Use this template:

```markdown
---
title: "Debug Report: [feature name] — Round [N]"
date: YYYY-MM-DD
feature: "[feature name]"
round: N
status: "[resolved / partially resolved / unresolved]"
---

# Debug Report: [feature name] — Round [N]

## Summary
[1-2 sentence overview of what was found and fixed]

## Issues Found

### Issue 1: [title]
**Symptom:** [what was observed]
**Root Cause:** [why it happened]
**Fix Applied:** [what was changed]
**Files Modified:**
- `path/to/file` — [description of change]
**Verification:** [how the fix was confirmed]

### Issue 2: [title]
...

## Remaining Issues
- [any items not yet resolved, to carry into next round]

## Recommendations for Next Round
- [suggestions for Phase ① discussion in the next iteration]
```

### Step 3: Record and Continue

- Update the main dev-loop task with the debug report path
- Record the round number and findings
- Return to **Phase ①** (Round N+1) with the debug report as context

---

## Loop Exit

When the user confirms the feature is deliverable:

### Final Summary

```markdown
## Dev-Loop Complete: [feature name]

**Rounds:** N
**Duration:** [start date] → [end date]

### What Was Built
[concise description of the delivered feature]

### Key Decisions
- [decision 1 and rationale]
- [decision 2 and rationale]

### Artifacts
- Plan: `docs/plans/YYYY-MM-DD-[feature-name].md`
- Debug Reports: [list of report paths, if any]
- Tests: [test file locations]

### Acceptance Criteria — Final Status
- [x] [criterion 1]
- [x] [criterion 2]
- [x] ...
```

Clean up dev-loop tasks (mark all as completed).

---

## Rules

1. **Never skip phases.** Even if an issue seems obvious, go through the full loop.
2. **Never skip root cause analysis.** Phase ④ must use systematic-debugging properly.
3. **Evidence before claims.** Phase ③ must produce actual verification evidence, not assumptions.
4. **User decides when to stop.** Only the user can declare a feature deliverable.
5. **Context carries forward.** Each round must reference previous rounds' findings.
6. **One phase at a time.** Complete each phase fully before moving to the next.
7. **Track state.** Use TaskCreate/TaskUpdate so progress survives context compression.
