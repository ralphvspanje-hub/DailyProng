# Stage 1: Exploration

You are an exploration agent. Your job is to fully understand the attached framework outline before anything gets built. You are NOT implementing. You are NOT planning. You are exploring, questioning, and resolving ambiguities.

## Your Process

1. Read the attached `learning-framework-outline.md` completely
2. Think through every section using the checklist below
3. Present your understanding back to me as a structured summary — prove you get it
4. List every question, ambiguity, or decision point you need me to resolve
5. We go back and forth until you have zero remaining questions

## What You Need to Figure Out

### Architecture & Structure
- Exactly which files need to be created, their purposes, and how they reference each other
- The folder structure and naming conventions
- **Multi-file architecture:** `CLAUDE.md` is the daily engine. `onboarding.md` is a separate file only loaded conditionally. Understand why this split exists (context window efficiency) and how the conditional loading works.
- How `CLAUDE.md` instructions map to each daily layer (theme cycles, adaptive difficulty, quality guard, feedback processing)
- How `CLAUDE.md` detects system state from `memory.md` and routes to the correct behavior
- **Mode boundaries within `CLAUDE.md`:** How does it detect which mode to activate? What are the exact trigger conditions for each mode? What's the priority order (e.g., pending feedback always takes priority over generation)? What's the default if the user says something ambiguous?
- **Cross-file handoff:** How does `CLAUDE.md` tell Cowork to read `onboarding.md`? What happens after onboarding completes — how does control return to `CLAUDE.md` behavior?

### The Onboarding Flow (lives in `onboarding.md`, NOT in `CLAUDE.md`)
- The exact conversation flow: what gets asked, in what order, how answers get synthesized into pillars/phases/topics
- How the onboarding agent proposes outputs (pillars, phases, topic map) and handles the user's review/adjustment loop
- What gets written to `memory.md` after onboarding is confirmed — including all structural sections needed for the daily engine to function
- How re-onboarding ("let's recalibrate" or equivalent intent) preserves existing progress while updating direction
- How `onboarding.md` is self-contained — it should work without needing any logic from `CLAUDE.md` beyond the initial handoff

### The Daily Generation Loop
- How the **pillar rotation algorithm** selects which pillar gets the next cycle (weighted random selection with no-repeat and starvation-prevention constraints — make sure you understand the specific rules)
- How the cycle structure (day types: Concept → Deep Dive → Case Study → Hands-On → Synthesis) adapts to different cycle lengths (3-day vs 5-day), including the scope compression rules for shorter cycles
- How the quality guard runs its 2 checks (freshness, prerequisites) before presenting a unit
- What exactly gets written to `memory.md` after each session, including the **archival rules** (progress log compression, feedback pattern overwriting)

### The Adaptive Difficulty Engine
- How difficulty levels per pillar get initialized during onboarding
- How the 6 feedback combinations (Too Easy/About Right/Too Hard × High/Medium/Low value) trigger specific adjustments
- **The two-consecutive-signals rule:** difficulty levels only change after two same-direction signals for the same pillar. Understand exactly when the streak resets and the exception case (Too Hard + Low Value triggers immediate action).
- Edge cases: what happens when all pillars hit level 5? When the user skips days? When feedback is inconsistent?

### Bridge Topics
- How the prerequisite check detects knowledge gaps
- How bridge topics pause and resume the current cycle
- How the 2-bridge-topic cap works (what happens when a third is needed)
- How `memory.md` tracks bridge state

### The Scheduled Task
- How a Cowork scheduled task generates a unit without user interaction
- **The pending feedback gate:** scheduled generation skips if feedback is outstanding. Understand why this matters for the adaptive difficulty engine.
- Where generated units get saved and how the system tracks which ones the user has/hasn't reviewed
- What happens when scheduled tasks run but the user hasn't given feedback on the previous unit yet (answer: it skips)

### Memory Management
- **The archival rules:** Progress Log keeps last 3 cycles in detail, older cycles get compressed to one-line summaries. Feedback Patterns stores one line per pillar (overwritten). Topics Covered groups older entries after 50+. Personal Notes capped at 10.
- The target size (~3,000 tokens after 6 months) and what happens if it's exceeded
- How `memory-backup.md` snapshots work — created at the start of each new cycle (not calendar week), overwrites previous backup
- How the `units/` folder can be used as a secondary recovery source

## Rules

- Do NOT assume any requirements or scope beyond what's explicitly described in the outline
- Do NOT start planning or implementing — exploration only
- Do NOT skip over sections you find ambiguous — flag them explicitly
- **If you think something in the framework outline is suboptimal, missing, or could be improved — flag it as a question.** Don't silently accept weak design choices. You're here to stress-test the design, not just understand it.
- Ask me questions in batches, grouped by topic area, not one at a time
- After each round of answers, summarize what's now resolved and what's still open

### Before Declaring Exploration Complete

Before you say "Exploration complete," run two self-checks:

1. **Completeness check:** Systematically walk through every section of the framework outline — Platform Prerequisites, Step Zero, all 5 Layers, the Daily Unit Format, Memory File (including archival rules), Topic Map (including bridge topics), and Implementation section — and confirm you have either resolved each section or determined it needs no clarification. List any section you skipped or glossed over.

2. **Write a Decision Summary:** Produce a concise written document titled "Exploration Summary" that lists every decision made, every ambiguity resolved, and any framework improvements agreed upon. This summary will be used by the plan stage as a consolidated reference, so it must be complete and standalone. Save it as a file in the working folder.

Only after both checks pass, tell me: **"Exploration complete. I'm ready for the plan stage."**

## Key Constraints You Should Know Upfront

- **Platform:** Cowork (Claude Desktop), Pro plan
- **Multi-file architecture:** `CLAUDE.md` is the daily engine (generation, quality guard, feedback, state detection). `onboarding.md` is a separate file only loaded when needed. This split keeps the daily context window lean.
- **`memory.md` is the only evolving file** — everything stateful lives here, with strict archival rules to prevent unbounded growth
- **The onboarding agent discovers everything** — no hardcoded pillars, phases, companies, or topics in any file; the prompts define HOW to discover and generate, not WHAT they are
- **Quality guard checks freshness and prerequisites** — not subjective quality. The user's feedback loop handles relevance and difficulty calibration.
- **Feedback is 2 words minimum** — the system must work even if the user just says "About Right, High"
- **Pending feedback blocks new generation** — the system never generates while feedback is outstanding, including scheduled tasks
- **State-aware startup** — the user shouldn't need specific trigger phrases for daily use. `CLAUDE.md` reads `memory.md` and determines the correct mode automatically. Recalibration uses intent detection, not a single magic phrase.

---

**Please confirm that you fully understand, then begin your exploration of the attached framework.**
