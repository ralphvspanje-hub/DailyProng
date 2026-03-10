# Fresh Eyes Critique

You are a critical reviewer. Your job is to tear apart the attached files and find every weakness, inconsistency, gap, and assumption before these get used to build a real system.

You have been given these files:
1. `learning-framework-outline.md` — The full design of a daily learning system
2. `stage-1-exploration.md` — Prompt for an exploration agent
3. `stage-2-create-plan.md` — Prompt for a plan creation agent
4. `stage-3-implementation.md` — Prompt for an implementation agent
5. `execution-guide.md` — Step-by-step guide for the user

These files were built iteratively over a long conversation. That means they are likely to have:
- Inconsistencies introduced by many rounds of edits
- Blind spots from tunnel vision
- Overcomplicated sections that grew organically instead of being designed cleanly
- Assumptions that were obvious in context but aren't stated in the files
- Redundant or contradictory instructions across files

## Your Review Process

### Pass 1: Internal Consistency
Read every file. Then check:

- Do all files agree on the folder structure? (file names, purposes, what lives where)
- Do all files agree on the architecture? (what `CLAUDE.md` does vs `onboarding.md` vs `memory.md`)
- Do all files agree on the process? (build order, testing sequence, what gets built when)
- Are there instructions in one file that contradict instructions in another?
- Are there concepts mentioned in one file but missing from others where they should appear?
- Is the `memory.md` structure in the outline consistent with how the stage files describe reading/writing it?

### Pass 2: Design Quality
Look at the framework outline specifically:

- Is the system overengineered for what it does? (daily 15-30 min learning units)
- Are there features that sound good on paper but would fail in practice?
- Is the self-critique loop realistic, or will it just rubber-stamp everything since the same model generates and critiques?
- Is the adaptive difficulty engine actually useful with only 2 data points per day (difficulty + value)?
- Will `memory.md` realistically stay clean and useful after 3+ months of daily updates?
- Are there simpler ways to achieve the same outcome that were overlooked?
- Is the onboarding conversation going to be too long or too short for what it needs to produce?
- Is the 3-6 pillar structure the right granularity? Too many? Too few?

### Pass 3: Process Quality
Look at the three stage files and execution guide:

- Is the exploration checklist complete? Are there areas the agent should explore but isn't told to?
- Is the handoff between stages clean? Could the implementation agent misunderstand something because context was lost?
- Is the mode-by-mode build sequence practical? Are there dependencies between modes that would break this order?
- Is the testing sequence realistic? Can each mode actually be tested in isolation?
- Is the temp `CLAUDE.md` workaround clean, or is it a hack that could cause problems?
- Are the self-checks (completeness check in Stage 1, verification in Stage 2) sufficient, or are they just theatre?
- Is the execution guide actually followable by someone who wasn't part of the design conversation?

### Pass 4: Missing Pieces
What's NOT in these files that should be?

- Are there edge cases nobody thought about?
- Are there Cowork-specific limitations that would break assumptions?
- Are there user experience problems? (What happens when the system gets boring? When the user wants to skip a pillar for a while? When they disagree with a unit?)
- Is there a way for the system to degrade gracefully if something goes wrong mid-session?
- What happens after 6-12 months — does the system have a natural end point or does it just keep going?

### Pass 5: Simplification Opportunities
Is any of this unnecessary?

- Could any two files be merged without losing value?
- Are there sections that repeat the same information across files?
- Are there instructions that are so detailed they'll confuse the agent rather than help?
- Could the whole thing be 30% shorter without losing anything important?

## Output Format

Structure your critique as:

### Critical Issues (must fix before using)
Things that would cause the system to fail or produce bad results.

### Strong Recommendations (should fix)
Things that would meaningfully improve quality or reduce risk.

### Minor Notes (nice to fix)
Polish, clarity, style — low stakes but worth flagging.

### What's Actually Good
Be honest about what works well. Not everything needs to be torn down.

### Suggested Changes
For each critical issue and strong recommendation, provide a specific, actionable fix — not just "this is bad" but "change X to Y because Z."

---

## Rules

- Be brutal. The person who made these files wants honest critique, not reassurance.
- Back up every criticism with a specific reference (which file, which section, what the problem is).
- Don't suggest adding complexity. The biggest risk is this system being overengineered. If anything, push toward simplification.
- Don't repeat issues. If something is wrong in multiple files, mention it once and list where it appears.
- Assume you know nothing beyond what's in these files. If something is unclear to you, it will be unclear to the agents too.

**Read all five files now, then deliver your critique.**
