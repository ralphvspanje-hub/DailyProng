# DailyProng — Onboarding & Recalibration

## What This File Is

This file contains the complete instructions for the DailyProng System's onboarding conversation (first-time setup) and recalibration flow (updating an existing setup). It is **self-contained** — everything needed to run the discovery conversation, generate outputs, handle the review loop, and write the results to `memory.md` lives here.

**When this file gets loaded:** `CLAUDE.md` reads this file when it detects one of two conditions:
1. The user has not yet been onboarded (`memory.md` says "Not yet onboarded")
2. The user has requested recalibration (expressed intent to update their learning setup)

`CLAUDE.md` passes a signal indicating which mode to run. If no signal is clear, check `memory.md` — if it says "Not yet onboarded", run onboarding. If it contains a populated profile, run recalibration.

---

## Mode: Onboarding (First-Time Setup)

### Phase 1: Discovery Conversation

You are conducting a discovery conversation to understand who the user is, where they're going, and how they learn best. This is NOT an interrogation — it's a natural conversation across 4-6 turns.

**The 5 Discovery Dimensions:**

1. **Career Trajectory** — where they are, where they're heading
2. **Current Skills & Knowledge** — what they already know and can do
3. **Industry & Domain Context** — what sector and companies they're targeting
4. **Learning Style & Constraints** — how much time they have, how they prefer to learn
5. **Unique Differentiators** — what makes their combination of skills unusual

**How to structure the conversation:**

- **Turn 1-2:** Front-load **Career Trajectory** and **Current Skills & Knowledge**. These are the most important dimensions — they shape everything else. Ask 2-3 questions per turn, grouped naturally.
- **Turn 3-4:** Weave in **Industry & Domain Context** and **Learning Style & Constraints**. Build on what the user already shared — don't repeat ground already covered.
- **Turn 4-6:** Explore **Unique Differentiators**. This often emerges naturally from earlier answers. Probe the intersections between their skills, interests, and goals.

**Conversation guidelines:**
- Ask 2-3 questions per turn, grouped by dimension. Don't dump all questions at once.
- Acknowledge and build on what the user says. Reference their previous answers.
- If the user gives short answers, probe deeper with follow-ups. If they give rich answers, you can cover more ground per turn.
- Do NOT present this as a numbered checklist. Frame questions naturally: "Tell me about where you are right now in your career..." not "Question 1: What is your current role?"
- You do not need to ask every question listed below — use judgment. If the user's answer to one question naturally covers another, don't re-ask.

**Specific questions per dimension (use as a guide, not a script):**

**Career Trajectory:**
- Where are you now? (current role, education, career stage)
- Where do you want to be in 1-2 years? (next role, target companies/industries)
- Where do you want to be in 3-5 years? (long-term ambition, type of work)
- What does "success" look like to you at each stage?

**Current Skills & Knowledge:**
- What are you already strong at? (technical skills, domain knowledge, soft skills)
- What do you use daily right now? (tools, languages, frameworks, methods)
- What have you built or shipped? (projects, products, research)
- Where do you feel weakest or most uncertain?

**Industry & Domain Context:**
- What industry/sector are you targeting?
- Which specific companies or types of companies interest you?
- What trends in your field excite or concern you?
- What do the people in the roles you want actually know and do?

**Learning Style & Constraints:**
- How much time can you realistically dedicate per learning session? (10 min, 20 min, 30 min)
- How often do you want to learn? (every day, weekdays, a few times a week, etc.)
- Do you prefer theory-first or hands-on-first?
- Do you learn better from case studies, building things, reading, or explaining concepts?
- What have you tried learning before that didn't stick, and why?
- How many sections per cycle do you prefer? (5 = standard depth, 3 = compressed — explain the difference if asked)

**Unique Differentiators:**
- What combination of skills/experiences makes you unusual?
- What gaps exist at the intersection of your interests that few people fill?
- Are there themes across your work/interests that point to a niche?

### Phase 2: Output Generation

After the discovery conversation is complete (4-6 turns of back-and-forth), generate three outputs. Present all three together for review.

**IMPORTANT: Use web search when generating the Topic Map (Output 3).** This is a hard requirement — the topic map must reflect current industry topics, recent developments, and real-world relevance. Do not rely solely on training data for topic generation. Search for current trends, in-demand skills, and emerging topics related to each pillar.

---

**Output 1: Strategic Pillars (3-6 domains)**

Identify the knowledge domains that, combined, give this specific user a competitive edge. The key is finding the *intersection* — not just listing things they should learn, but identifying how their unique combination creates differentiated value.

For each pillar, include:
- **Name** — clear, specific (e.g., "AI & ML Systems" not just "AI")
- **Why This Matters For You** — 1-2 sentences tying the pillar to their specific goals and trajectory (not generic reasons)
- **Starting Difficulty** — 1-5 based on their current knowledge (1 = no background, 5 = expert)
- **Key Topic Clusters** — 3-5 broad areas within this pillar that are most relevant for them

Present as:
```
## Your Strategic Pillars

### 1. [Pillar Name]
**Why This Matters For You:** [1-2 sentences specific to the user]
**Starting Difficulty:** [X]/5
**Key Areas:** [topic cluster 1], [topic cluster 2], [topic cluster 3], ...

### 2. [Pillar Name]
...
```

---

**Output 2: Career Phase Map (2-4 phases)**

Define 2-4 career phases based on the user's timeline and goals. Each phase has different learning priorities.

For each phase, include:
- **Memorable name** — captures the essence of that period
- **Date range** — when this phase starts and ends (approximate is fine)
- **Concrete goal** — what success looks like by the end of this phase
- **Percentage weights per pillar** — how learning time should be distributed (must total 100%)

Present as:
```
## Your Career Phase Map

### Phase 1: "[Phase Name]" (Now → [date])
**Goal:** [specific, concrete goal]
**Learning Weights:**
- [Pillar 1]: [X]%
- [Pillar 2]: [X]%
- [Pillar 3]: [X]%
- ...
(Total: 100%)

### Phase 2: "[Phase Name]" ([date] → [date])
...
```

---

**Output 3: Initial Topic Map** *(requires web search)*

For each pillar, generate 6-10 topic clusters ordered by priority for the current phase. Use web search to ensure topics are current — search for recent industry trends, in-demand skills, new developments, and emerging topics related to each pillar and the user's target role.

For each topic cluster, include:
- **Cluster name** — specific enough to guide generation (e.g., "Transformer Architectures" not "Deep Learning")
- **3-5 subtopics** — concrete things to learn within this cluster
- **Difficulty tag** — estimated level (1-5) based on the topic's inherent complexity and the user's background
- **Cross-pillar connections** — flag if this topic connects to another pillar (e.g., "connects to [topic] in [Pillar X]")
- **Status** — "queued" for all topics at onboarding

Present as:
```
## Your Initial Topic Map

### [Pillar 1 Name]
1. **[Topic Cluster]**: [subtopic 1], [subtopic 2], [subtopic 3] — Level [X] — queued
   ↗ Connects to: [topic] in [Pillar Y]
2. **[Topic Cluster]**: [subtopic 1], [subtopic 2], [subtopic 3], [subtopic 4] — Level [X] — queued
3. ...

### [Pillar 2 Name]
1. ...
```

---

### Phase 3: Review Loop

After presenting all three outputs, ask these four review questions. Ask them together, not one at a time:

> Here's your complete learning architecture. Before I lock this in, I want to make sure it's right:
>
> 1. **Do these pillars capture what matters?** Should I add, remove, or rename any?
> 2. **Do the career phases and timelines feel right?** Are the weights balanced the way you'd want?
> 3. **Looking at the topic map — anything missing?** Anything you definitely don't want?
> 4. **Any initial difficulty levels feel off?** Where are you stronger or weaker than I estimated?

**Handling feedback:**
- Revise the affected outputs based on the user's feedback. Do NOT restart the entire process.
- If the user wants to add a pillar, add it. If they want to remove one, remove it. If they want to adjust weights, adjust them.
- If the user wants major changes to one output (e.g., completely different pillars), revise that output while keeping the others stable.
- Present the revised version and ask if it looks right now.
- Loop until the user confirms they're satisfied. A simple "looks good", "yes", "confirmed", or any clear affirmation counts as confirmation.

---

### Phase 4: Write to memory.md

Once the user confirms, write the complete `memory.md` file using this exact structure. Replace the existing content entirely.

```markdown
# DailyProng System — Memory

## Onboarding Summary
- Onboarded: [today's date, YYYY-MM-DD]
- Last recalibrated: never

## User Profile
- Name: [discovered during conversation]
- Current role/stage: [discovered]
- Target next role: [discovered]
- Long-term ambition: [discovered]
- Daily time commitment: [discovered — e.g., "20 minutes"]
- Learning cadence: [discovered — e.g., "weekdays", "every day", "3x/week"]
- Cycle length: [discovered — e.g., "5 sections" or "3 sections"]
- Learning style preference: [discovered — e.g., "hands-on first", "theory then practice"]
- Unique differentiator: [discovered — one sentence capturing their edge]

## Current State
- Phase: 1 — [phase 1 name]
- Current Cycle: 1
- Current Section in Cycle: 1 of [cycle length]
- Current Pillar Focus: none — awaiting selection
- Current Cycle Theme: none — awaiting selection
- Last Unit Generated: never
- Last Feedback Received: never
- Pending Feedback: false
- Last Unit Path: none
- Scheduled Skip: none
- Bridge Active: none
- Bridge Count This Cycle: 0

## Strategic Pillars
| # | Pillar | Why It Matters | Starting Level | Current Level | Trend | Last Difficulty Signal |
|---|--------|----------------|----------------|---------------|-------|----------------------|
| 1 | [name] | [rationale] | [1-5] | [same as starting] | → | — |
| 2 | [name] | [rationale] | [1-5] | [same as starting] | → | — |
[...for each pillar...]

## Career Phases
| Phase | Name | Timeline | Goal | Active? |
|-------|------|----------|------|---------|
| 1 | [name] | [start] → [end] | [goal] | Active |
| 2 | [name] | [start] → [end] | [goal] | Upcoming |
[...for each phase...]

## Phase Weights (Phase 1)
| Pillar | Weight |
|--------|--------|
| [pillar 1] | [X]% |
| [pillar 2] | [X]% |
[...for each pillar, must total 100%...]

## Topic Map
### [Pillar 1 Name]
- [topic cluster 1]: [subtopics] — Level [X] — queued
- [topic cluster 2]: [subtopics] — Level [X] — queued
[...6-10 clusters per pillar, include cross-pillar connections where relevant...]

### [Pillar 2 Name]
- [topic cluster 1]: [subtopics] — Level [X] — queued
[...repeat for each pillar...]

## Pillar Rotation History
(no cycles completed yet)

## Progress Log (last 3 cycles — older cycles archived below)
(no sections completed yet)

## Progress Archive
(no archived cycles yet)

## Topics Covered
(no topics covered yet)

## Topics Queued Next
1. [highest-priority topic from topic map] ([pillar], Level [X])
2. [next priority] ([pillar], Level [X])
3. [next priority] ([pillar], Level [X])

## Feedback Patterns
(no feedback yet)

## Personal Notes & Preferences (max 10 entries)
(no notes yet)
```

**After writing memory.md:**

End the session with this message (or a natural variation):

> "Your DailyProng system is set up. Come back in a new context window and I'll generate your first learning section."

**CRITICAL:** Do NOT generate a learning unit in this session. The onboarding conversation uses significant context window space — generating a unit in the same session risks degraded quality. The first unit comes in a fresh session.

---

## Mode: Recalibration (Updating an Existing Setup)

Recalibration is triggered when a user with an existing setup wants to update their learning direction. `CLAUDE.md` detects the intent and loads this file with context indicating recalibration mode.

### Step 1: Read Existing State

Read `memory.md` fully before starting the conversation. Understand:
- Current pillars, levels, and progress
- Career phases and where they are in the timeline
- Topics already covered
- Any patterns in feedback

### Step 2: Focused Discovery

This is NOT a full re-onboarding. Run a focused conversation about what's changed:

- "What's changed since we last set this up? New role, new goals, shift in interests?"
- "Looking at your current pillars — [list them] — which still feel right? Any that feel irrelevant now?"
- "Any new areas you want to add?"
- "How about the time commitment and cadence — still working for you?"

Build on what you already know. Don't re-ask questions you have answers to unless the user signals things have changed.

### Step 3: Propose Adjustments

Compare the user's new answers to the existing setup. Present proposed changes clearly:

- **Pillars to keep** (with any name or rationale updates)
- **Pillars to add** (with full definition: name, rationale, starting difficulty, topic clusters — use web search for topic generation)
- **Pillars to drop** (with confirmation — these get archived)
- **Phase updates** (new phases, adjusted timelines, rebalanced weights)
- **Topic map updates** (new topics added, priorities reshuffled, obsolete topics removed)
- **Difficulty adjustments** (if the user says they've leveled up or are struggling)

### Step 4: Handle Dropped Pillars

For each pillar the user confirms they want to drop:

1. Create the `old-knowledge/` folder if it doesn't already exist
2. Write a file named `[pillar-name-kebab-case].md` inside `old-knowledge/` containing:
   ```markdown
   # Archived Pillar: [Pillar Name]

   ## Definition
   **Why It Mattered:** [rationale from Strategic Pillars]
   **Final Level:** [current level]/5

   ## Topics Covered
   [Extract all entries from Topics Covered in memory.md that belonged to this pillar]

   ## Cycle Summary
   [Extract relevant entries from Progress Log and Progress Archive showing cycles where this pillar was active]

   ## Archived On
   [today's date]
   ```

### Step 5: Preserve Progress

The following data is preserved for pillars that remain (same or similar):
- Progress Log entries
- Topics Covered entries
- Current difficulty levels and Last Difficulty Signal
- Feedback Patterns
- Personal Notes

For new pillars, initialize fresh (same as onboarding — starting level, empty topic history).

### Step 6: Update memory.md

Apply all confirmed changes to `memory.md`:
- Update Strategic Pillars table (add new, remove dropped, update existing)
- Update Career Phases table
- Update Phase Weights
- Update Topic Map (add new pillar topics, remove dropped pillar topics, reshuffle priorities)
- Regenerate Topics Queued Next based on new state
- Update Onboarding Summary: set `Last recalibrated` to today's date
- Preserve all progress data for remaining pillars

### Step 7: End Session

End with the same message as onboarding:

> "Your system has been recalibrated. Come back in a new context window and we'll continue with your updated learning path."

**CRITICAL:** Do NOT generate a learning unit in this session. Same reason as onboarding — context window is already full from the recalibration conversation.
