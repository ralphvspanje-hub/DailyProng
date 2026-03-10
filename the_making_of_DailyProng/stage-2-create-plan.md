# Stage 2: Create the Implementation Plan

You have completed the exploration stage. You now have full clarity on the framework, all ambiguities are resolved, and all decisions are made. You also produced an Exploration Summary document with all decisions consolidated.

Now produce a markdown implementation plan. This plan will be handed to a **separate implementation agent with a clean context window** — so it must be completely self-contained. The implementation agent will have this plan + the framework outline + the Exploration Summary (all in the same folder). However, it will NOT have access to our exploration conversation. Every critical decision must be embedded in the plan itself — don't assume the agent will cross-reference the Exploration Summary for implementation-critical details.

**Use the Exploration Summary as your primary reference** when writing the Critical Decisions and Resolved Ambiguities sections. Cross-check it against the full conversation to ensure nothing was missed.

## Requirements

- Clear, minimal, concise steps — no fluff
- Every step must be actionable by an agent that wasn't part of our exploration
- Include ALL decisions, rationale, and resolved ambiguities from our exploration — nothing should be left implicit
- Track status of each step with emojis:
  - 🟩 Done
  - 🟨 In Progress
  - 🟥 To Do
- Include overall progress percentage at the top
- Do NOT add extra scope or unnecessary complexity beyond what we explicitly clarified
- Steps should be modular so the implementation agent can execute and verify one at a time

### Mandatory: Files Must Be Built and Tested Incrementally

The system has two instruction files: `onboarding.md` (one-time/rare use) and `CLAUDE.md` (daily engine with multiple modes). Both must be built incrementally — NOT as single monolithic writes.

**`onboarding.md` is built and tested first, as its own step:**
1. **Step N:** Write `onboarding.md` — the full discovery conversation flow, output generation (pillars, phases, topic map), review loop, and writing to memory.md. Also handles recalibration mode.
   - **Testing workaround:** Since Cowork reads `CLAUDE.md` as its instruction file and `CLAUDE.md` doesn't exist yet, the plan must include creating a **temporary minimal `CLAUDE.md`** that provides enough context for Claude to act naturally — e.g., "You are the Powerhouse learning system. The user needs onboarding. Read `onboarding.md` — it contains the discovery conversation flow — and begin the onboarding conversation with the user." This lets you test onboarding in isolation. It gets replaced with the real `CLAUDE.md` in the next step.
   - **Verification:** User points Cowork at the folder with blank memory.md + temp CLAUDE.md, types anything, and onboarding starts. Test the full flow end-to-end.

**Then `CLAUDE.md` is built mode-by-mode:**
2. **Step N+1:** Write `CLAUDE.md` scaffold — file header, state detection logic (reads memory.md, determines mode, conditionally loads onboarding.md), memory backup trigger at cycle start
3. **Step N+2:** Write Daily Generation Mode — pillar rotation algorithm (weighted random with no-repeat and starvation constraints) + cycle day structure + unit format
4. **Step N+3:** Write Quality Guard — the 2 checks (freshness/non-repetition, prerequisite check) + bridge topic insertion logic
5. **Step N+4:** Write Feedback Processing Mode — parsing feedback + two-consecutive-signals rule for difficulty adjustment + memory archival after cycle completion + updating memory.md
6. **Step N+5:** Write Scheduled Task Mode — autonomous generation with pending feedback gate (skip if feedback outstanding)

Each step must have its own verification that tests ONLY that mode/file. The implementation agent will test each one in Cowork before moving to the next. This prevents a broken system that fails in ways that are impossible to debug.

## Template

```markdown
# Powerhouse System — Implementation Plan

**Overall Progress:** `0%`

## TLDR
[One paragraph: what we're building, why, and how it works at a high level]

## Target Environment
- Platform: Cowork (Claude Desktop)
- Subscription: Pro
- Working folder: powerhouse-system/
- Key constraint: [any Cowork-specific limitations that affect implementation]
- Prerequisites verified: [list which platform prerequisites from the outline were confirmed during exploration]

## Critical Decisions
Every architectural and implementation choice made during exploration. The implementation agent MUST follow these — they are not suggestions.

- **Decision 1:** [choice] — [rationale]
- **Decision 2:** [choice] — [rationale]
- ...

## File Manifest
Every file that will be created, with its purpose and who/what creates it:

| File | Purpose | Created By | Notes |
|------|---------|------------|-------|
| `CLAUDE.md` | Daily engine: state detection, generation, quality guard, feedback | Implementation agent | Does NOT contain onboarding logic |
| `onboarding.md` | Onboarding & recalibration instructions | Implementation agent | Only loaded conditionally |
| `memory.md` | Starter memory (pre-onboarding) | Implementation agent | Minimal scaffold only |
| ... | ... | ... | ... |

## Resolved Ambiguities
Questions that came up during exploration and how they were resolved. The implementation agent should not re-ask these.

| Question | Resolution |
|----------|------------|
| [question from exploration] | [answer/decision] |
| ... | ... |

## Tasks

- [ ] 🟥 **Step 1: [Name]**
  Brief description of what this step accomplishes.
  - [ ] 🟥 Subtask 1: [specific action]
  - [ ] 🟥 Subtask 2: [specific action]
  **Verification:** [How to confirm this step is done correctly]

- [ ] 🟥 **Step 2: [Name]**
  Brief description.
  - [ ] 🟥 Subtask 1: [specific action]
  - [ ] 🟥 Subtask 2: [specific action]
  **Verification:** [How to confirm this step is done correctly]

...

## Testing Checklist
After all steps are complete, verify:
- [ ] Onboarding flow works end-to-end
- [ ] Daily unit generation produces correct output with proper unit format
- [ ] Pillar rotation follows weighted random selection with no-repeat and starvation constraints
- [ ] Quality guard catches repeated topics (freshness check)
- [ ] Quality guard catches missing prerequisites (prerequisite check)
- [ ] Bridge topics pause and resume cycles correctly
- [ ] Feedback updates memory correctly
- [ ] Two-consecutive-signals rule for difficulty adjustment works
- [ ] "Too Hard + Low Value" triggers immediate pillar switch
- [ ] Scheduled task skips generation when feedback is pending
- [ ] Re-onboarding preserves progress
- [ ] Memory archival compresses old progress logs after cycle completion
- [ ] Memory stays under ~3,000 tokens after simulated extended use
- [ ] Memory backup snapshot gets created at cycle start
- [ ] Compressed cycles (3-day) produce properly scoped units
```

## Before Presenting the Plan

Before you show me the plan, run this verification:

1. **Decision completeness:** Cross-reference the Exploration Summary against the plan's Critical Decisions and Resolved Ambiguities sections. Every item in the summary must appear in the plan. Flag any gaps.
2. **Outline coverage:** Walk through every section of `learning-framework-outline.md` (Platform Prerequisites, Step Zero, all 5 Layers, Daily Unit Format, Memory File with archival rules, Topic Map with bridge topics, Implementation) and confirm each maps to at least one implementation step. Flag any section with no corresponding step.
3. **No orphan decisions:** Check that no exploration question was left unresolved — if it was, it should appear in the plan as an explicit "deferred to implementation agent" note, not silently dropped.

If any check fails, fix the plan before presenting it.

## Output

Save the completed plan as `implementation-plan.md` in the working folder.

**Do NOT start implementing. Just write the plan.**
