# Daily Learning Framework: "The DailyProng System"

## How This Works

This framework is a **goal-driven learning engine** that starts by understanding YOU — your ambitions, your current skills, your timeline — and builds a personalized learning architecture from that foundation.

Every day, you receive ONE focused learning unit (~15-30 min) that is:
- Connected to a **strategic theme** for that cycle
- Mapped to one of your **career phases**
- Building on what you learned previously (compounding, not scattered)
- Calibrated to your **current difficulty level** (adapts based on your feedback)
- Quality-checked by a **freshness and prerequisite guard** before it reaches you

---

## Platform Prerequisites

Before building this system, verify these Cowork capabilities:

1. **File system read/write** — Claude can read and write `.md` files in the working folder
2. **Conditional file reading** — `CLAUDE.md` can instruct Claude to read and act on `onboarding.md` mid-session. **Test this before building:** create a dummy `CLAUDE.md` that says "You are a quiz system. Your quiz questions are stored in `test.md`. When the user starts a session, read `test.md` and begin the quiz with the user." Put a few trivia questions in `test.md`. Open Cowork, type "go", and verify Claude reads the file and starts quizzing you directly.
3. **Multiple sessions** — you can open separate Cowork sessions pointed at the same folder (needed for testing during implementation)
4. **Scheduled tasks** — the `/schedule` command works for recurring automated runs (needed only for autopilot mode; the core system works without this)

If prerequisite #2 fails, the onboarding logic must be inlined into `CLAUDE.md` behind a conditional instead of living in a separate file. This costs context window space but guarantees compliance.

---

## Step Zero: The Onboarding Agent

Before the system generates a single learning unit, it needs to understand who you are and where you're going. The onboarding agent runs ONCE (your first session) and builds the entire learning architecture from your answers.

> **Architecture note:** The onboarding logic lives in `onboarding.md`, separate from the daily engine (`CLAUDE.md`). This keeps the daily system prompt lean — onboarding instructions are only loaded when needed (first run or recalibration) and are invisible during normal daily use.

### What the Onboarding Agent Does

It conducts a structured discovery conversation across 5 dimensions:

**1. Career Trajectory**
- Where are you now? (current role, education, stage)
- Where do you want to be in 1-2 years? (next role, target companies, industries)
- Where do you want to be in 3-5 years? (long-term ambition, type of work)
- What does "success" look like to you at each stage?

**2. Current Skills & Knowledge**
- What are you already strong at? (technical skills, domain knowledge, soft skills)
- What do you use daily right now? (tools, languages, frameworks, methods)
- What have you built or shipped? (projects, products, research)
- Where do you feel weakest or most uncertain?

**3. Industry & Domain Context**
- What industry/sector are you targeting?
- Which specific companies or types of companies interest you?
- What trends in your field excite or concern you?
- What do the people in the roles you want actually know and do?

**4. Learning Style & Constraints**
- How much time can you realistically dedicate per learning day? (10 min, 20 min, 30 min)
- Which days do you want to learn? (Mon-Fri, every day, specific days, X times per week)
- Do you prefer theory-first or hands-on-first?
- Do you learn better from case studies, building things, reading, or explaining concepts?
- What have you tried learning before that didn't stick, and why?

**5. Unique Differentiators**
- What combination of skills/experiences makes you unusual?
- What gaps exist at the intersection of your interests that few people fill?
- Are there themes across your work/interests that point to a niche?

### What the Onboarding Agent Produces

After the conversation, it generates three outputs for your review:

**Output 1: Your Strategic Pillars (3-6 domains)**
These are the knowledge domains that, combined, make you uniquely valuable. The agent identifies the *intersection* that gives you an edge.

Example output (for illustration only — yours will be different):
```
Proposed Pillars:
1. AI & ML Systems — [reason tied to your goals]
2. Data Product Thinking — [reason tied to your goals]
3. Tech Business & Strategy — [reason tied to your goals]
4. Regulatory & Governance — [reason tied to your goals]
5. Engineering Fluency — [reason tied to your goals]

Each pillar includes:
- Why it matters for YOUR specific trajectory
- Your estimated starting difficulty level (1-5)
- Key topic clusters within the pillar
```

**Output 2: Your Career Phase Map**
A phased timeline with weighted emphasis per pillar for each phase. Phases are named, dated, and tied to concrete goals.

Example output:
```
Phase 1: "[Phase Name]" (Now → [date])
  Goal: [specific goal]
  Weights: Pillar A (35%), Pillar B (25%), Pillar C (20%), Pillar D (15%), Pillar E (5%)

Phase 2: "[Phase Name]" ([date] → [date])
  Goal: [specific goal]
  Weights: [adjusted weights based on phase goal]
```

**Output 3: Your Initial Topic Map**
A structured list of specific topics within each pillar, organized by difficulty level and ordered by priority for the current phase.

> **Quality note:** The onboarding agent should use web search when generating the topic map to ensure topics are current and industry-relevant — not just drawn from training data. This is especially important for fast-moving fields.

### The Review Loop

After presenting these three outputs, the onboarding agent asks:

1. "Do these pillars capture what matters? Should I add, remove, or rename any?"
2. "Do the career phases and timelines feel right? Are the weights balanced?"
3. "Looking at the topic map — anything missing? Anything you definitely don't want?"
4. "Any initial difficulty levels feel off? Where are you stronger/weaker than I estimated?"

You adjust. The agent revises. Once you confirm, it writes everything into `memory.md` and the system is ready to generate its first learning unit.

### Re-Onboarding

Life changes. You can trigger a re-onboarding at any time by expressing that you want to update your learning setup — "let's recalibrate", "I want to update my goals", "my priorities changed", or any clear signal. `CLAUDE.md` detects the intent (not a single magic phrase) and loads `onboarding.md`. The agent re-runs the discovery, compares your new answers to the existing setup, and proposes adjustments — preserving your progress history while updating the direction.

---

## The Five Layers

### Layer 1: Your Strategic Pillars (discovered during onboarding)
These are the 3-6 domains identified by the onboarding agent. They are personal to you — not a generic template. Each pillar has:
- A clear name and description
- A "Why This Matters For You" rationale tied to your specific goals
- An independent difficulty level (1-5)
- A set of topic clusters ordered by priority

### Layer 2: Career Phase Mapping (discovered during onboarding)
Each pillar gets weighted differently depending on which career phase you're optimizing for. The onboarding agent defines these phases based on your goals and timeline. The system shifts emphasis automatically as you move between phases.

Phases are structured as:
- A memorable name
- A date range
- A concrete goal
- Percentage weights per pillar (must total 100%)

You can have 2-4 phases. When you hit a phase transition (new job, graduation, promotion), you tell the system and it shifts the weights accordingly.

### Layer 3: Theme Cycles (cadence discovered during onboarding)
Instead of random daily topics, each cycle has a **theme** drawn from one pillar. Themes rotate across pillars based on the current phase weighting. Within a cycle, each day builds on the previous one.

The onboarding agent discovers the user's preferred cadence:
- Which days per week? (e.g., Mon-Fri, weekdays only, every day, 3x/week)
- How many learning days per cycle? (e.g., 5-day cycle, 3-day cycle)

#### Pillar Rotation Algorithm

Pillar selection for each new cycle uses **weighted random selection** based on current phase weights, with two constraints:
1. **No back-to-back repeats** — the same pillar cannot run two consecutive cycles, unless it has >50% weight in the current phase
2. **Starvation prevention** — if any pillar hasn't appeared in the last `(number of pillars)` cycles, it gets priority for the next cycle regardless of weight

This ensures high-weight pillars appear frequently while low-weight pillars don't get permanently starved.

#### Day Types Within a Cycle

The day types follow a progression that scales to the cycle length:

**5-day cycle (standard):**

| Day | Type | What Happens |
|---|---|---|
| Day 1 | **Concept** — Learn a core idea | Core concept introduction, tied to a relevant company/industry |
| Day 2 | **Deep Dive** — Go technical | Technical details, trade-offs, how it actually works under the hood |
| Day 3 | **Case Study** — See it in practice | Real company example — how they built it, what went wrong, what worked |
| Day 4 | **Hands-On** — Build or experiment | Build something small, analyze a dataset, or prototype an approach |
| Day 5 | **Synthesis** — Connect to your goals | Connect the cycle's learning to your specific career goals or interview scenarios |

**3-day cycle (compressed):**

| Day | Type | Scope Note |
|---|---|---|
| Day 1 | **Concept + Deep Dive** | Concept gets ~60% of time, deep dive ~40%. Focus on the single most important technical dimension rather than full coverage. |
| Day 2 | **Case Study + Hands-On** | Case study abbreviated to key lessons. Exercise scoped smaller — analysis or explanation rather than building. |
| Day 3 | **Synthesis** | Same as 5-day version. |

> **Important for compressed cycles:** The unit stays within the normal 15-30 min target. Compression means *narrower scope per day*, not *longer sessions*. The system picks one core thread from each combined type rather than trying to cover both fully.

The cycle length and day mapping are stored in `memory.md` and can be adjusted at any time.

### Layer 4: Adaptive Difficulty Engine
The system asks you for feedback after every unit and adjusts accordingly.

**How it works:**

After each daily unit, you answer two quick questions:
1. **Difficulty** — "How did this feel?" → Too Easy / About Right / Too Hard
2. **Value** — "Was this useful?" → High / Medium / Low

These responses get logged in `memory.md` and the system uses them to calibrate the next unit:

| Your Feedback | What Happens Next |
|---|---|
| Too Easy + High Value | Keep the topic area, but go deeper — more technical, more edge cases |
| Too Easy + Low Value | Skip ahead in this pillar — move to the next topic cluster |
| About Right + High Value | Perfect. Continue the current trajectory, slight progression |
| About Right + Low Value | Same difficulty, but pivot the angle — different framing or application |
| Too Hard + High Value | Interested but struggling. Break it down: add prerequisites, more examples |
| Too Hard + Low Value | Not landing. Park it for later, switch to a different pillar for the rest of the cycle |

**Difficulty levels per pillar (tracked independently):**

Each pillar has its own difficulty setting on a 1-5 scale. They move independently based on your feedback.

```
Difficulty Scale:
1 — Fundamentals: "What is X and why does it matter?"
2 — Applied Basics: "How does X work in practice?"
3 — Intermediate: "What are the trade-offs and edge cases of X?"
4 — Advanced: "How would you design/build/improve X from scratch?"
5 — Expert: "What's broken about how the industry does X and what's next?"
```

**When do levels actually change?**
- A single "Too Easy" or "Too Hard" does NOT immediately change the level. It takes **two consecutive same-direction signals** for the same pillar to trigger a level shift.
- "About Right" resets the streak — one "Too Easy" followed by "About Right" means the level stays put.
- **Exception:** "Too Hard + Low Value" triggers an immediate response (park the topic, switch pillar) because the user is both struggling and disengaged.

**Edge case — all pillars at level 5:** The system shifts to synthesis, debate, and frontier-exploration units. The difficulty label changes to "5/5 — Expert Mode" and units focus on cross-pillar connections, open questions in the field, and contrarian analysis.

### Layer 5: Quality Guard (Freshness & Prerequisite Check)

Before any learning unit reaches you, the system runs two concrete checks focused on what can actually be verified:

> **Design note:** Subjective quality checks (relevance, difficulty calibration) were intentionally omitted. The same model that generates a unit is biased toward judging its own output favorably. Those qualities are better caught by the user's feedback (Layer 4). The quality guard only checks things it can verify against data in `memory.md`.

**Check 1: Freshness & Non-Repetition**
*"Have I already covered this? Am I recycling the same framing?"*
- Scans the Topics Covered log and recent Progress Log in `memory.md` for overlapping topics
- Checks if examples and case studies have been used before
- If overlap detected → find a new angle or skip to the next topic in the sequence

**Check 2: Prerequisite Check**
*"Does this unit assume knowledge the user hasn't been taught yet?"*
- Cross-references the unit's assumed knowledge against Topics Covered
- If a gap is found → inserts a bridge topic before this unit (see Topic Map section)

**What appears in the unit header:**
```
Quality Guard: Freshness ✓ | Prerequisites ✓
```

---

## The Daily Unit Format

Each daily learning unit follows a consistent structure:

```
📅 Day X of [cycle length] | Cycle Theme: [theme] | Pillar: [pillar]
Phase: [phase number] — [phase name]
Difficulty Level: [pillar level, e.g. 3/5]
Quality Guard: Freshness ✓ | Prerequisites ✓

## Today's Focus
[Clear, specific topic — one sentence]

## Why This Matters For You
[2-3 sentences connecting this to your specific goals]

## The Core Idea (~10 min read)
[Explanation, with links to sources if relevant]

## Go Deeper (Optional)
[One article, paper, video, or repo worth exploring]

## Quick Exercise (~5-10 min)
[Something concrete: write, build, analyze, or explain]

## Connection Point
[How this links to the previous unit and the next one]

---

## 📊 Your Feedback (answer after completing the unit)
**Difficulty:** Too Easy / About Right / Too Hard
**Value:** High / Medium / Low
**Optional note:** [anything you want the system to know]
```

---

## The Memory File (`memory.md`)

This is the brain of the system. It's a single markdown file that Claude reads at the start of every session and updates after every unit. Everything the system "remembers" lives here.

### Structure of `memory.md`:

```markdown
# DailyProng System — Memory

## Onboarding Summary
- Onboarded: [date]
- Last recalibrated: [date or "never"]

## User Profile
- Name: [discovered]
- Current role/stage: [discovered]
- Target next role: [discovered]
- Long-term ambition: [discovered]
- Daily time commitment: [discovered]
- Learning cadence: [discovered — e.g., "Mon-Fri", "every day", "3x/week"]
- Cycle length: [discovered — e.g., 5 days, 3 days]
- Learning style preference: [discovered]
- Unique differentiator: [discovered]

## Current State
- Phase: [phase number] — [phase name]
- Current Cycle: [number]
- Current Day in Cycle: [day number, e.g. 3 of 5]
- Current Pillar Focus: [pillar name]
- Current Cycle Theme: [theme]
- Last Unit Generated: [YYYY-MM-DD or "never"]
- Last Feedback Received: [YYYY-MM-DD or "never"]
- Pending Feedback: [true/false]
- Bridge Active: [none / "topic X → prerequisite for topic Y"]

## Strategic Pillars (generated from onboarding)
| # | Pillar | Why It Matters | Starting Level | Current Level | Trend |
|---|---|---|---|---|---|
| 1 | [discovered] | [discovered] | [1-5] | [1-5] | [↑→↓] |
| ... | ... | ... | ... | ... | ... |

## Career Phases (generated from onboarding)
| Phase | Name | Timeline | Goal | Active? |
|---|---|---|---|---|
| 1 | [discovered] | [start] → [end] | [discovered] | ✅ |
| ... | ... | ... | ... | ... |

## Phase Weights (current phase)
| Pillar | Weight |
|---|---|
| [pillar 1] | [X]% |
| ... | ... |

## Topic Map
### [Pillar 1 Name]
- [topic cluster 1]: [subtopics] — Level [X] — [covered/queued/in progress]
[...generated per pillar...]

## Pillar Rotation History
[last (number of pillars) cycles only]
- Cycle [N]: [pillar name]
- Cycle [N-1]: [pillar name]

## Progress Log (last 3 cycles — older cycles archived below)
### Cycle [N] — [theme] ([pillar])
- Day 1: [topic] → Difficulty: [response] | Value: [response]
- Day 2: [topic] → Difficulty: [response] | Value: [response]

## Progress Archive (one-line summaries of older cycles)
- Cycle 1: [theme] ([pillar]) — Avg difficulty: [X] | Avg value: [X] | Level change: [none/+1/-1]

## Topics Covered (for freshness check)
- [topic]: [subtopics covered] (Cycle [N])
[after 50+ entries, group older ones: "Cycles 1-10: [pillar] — [topics]"]

## Topics Queued Next
1. [topic] ([pillar], Level [X])
2. [topic] ([pillar], Level [X])

## Feedback Patterns (one line per pillar — overwritten, not appended)
- [Pillar name]: [current pattern summary]

## Personal Notes & Preferences (max 10 entries)
- [accumulated from optional feedback notes]
```

### Memory Archival Rules

`memory.md` must stay lean to fit in the context window effectively. These rules are enforced automatically:

1. **Progress Log** retains only the **last 3 completed cycles** in full detail. When a new cycle completes, the oldest detailed cycle gets compressed to a one-line summary and moved to Progress Archive.
2. **Feedback Patterns** stores exactly **one summary line per pillar** — overwritten each time, not appended.
3. **Topics Covered** grows indefinitely but each entry is one line. After 50+ entries, older entries get grouped: `"Cycles 1-10: [pillar] — [comma-separated topics]"`.
4. **Pillar Rotation History** stores only the last `(number of pillars)` entries.
5. **Personal Notes** capped at 10 entries. When full, the system asks which to replace.

**Target size:** A healthy `memory.md` should stay under ~3,000 tokens after 6 months. If it exceeds this, the system flags it and compresses the progress archive further.

### How Memory Gets Updated

1. **Start of session** — `CLAUDE.md` reads `memory.md` to determine system state
2. **If not onboarded** — loads `onboarding.md`, runs discovery, writes results to `memory.md`
3. **If onboarded** — generates today's unit, runs quality guard, presents it
4. **You complete the unit** — give your feedback (difficulty + value + optional note)
5. **End of session** — Claude updates `memory.md` — progress, feedback, difficulty adjustments, archival if a cycle just completed

### Memory Backup

At the **start of each new cycle** (first day of a new theme cycle), the system copies `memory.md` to `memory-backup.md`, overwriting the previous backup. Worst-case data loss: one cycle.

### Memory Recovery

If `memory.md` gets corrupted or accidentally deleted:
1. **From `memory-backup.md`** — copy it back. You lose at most one cycle of progress.
2. **From `units/` folder** — past units contain pillar names, difficulty levels, themes, and dates. The system can scan these to reconstruct progress.
3. **From re-onboarding** — if everything is lost, trigger recalibration to rebuild from scratch.

---

## Topic Map (generated by onboarding agent)

The topic map is NOT pre-defined. The onboarding agent generates it based on your specific pillars, career goals, and current knowledge level. For each pillar, it produces:

- 6-10 topic clusters, ordered by priority for the current phase
- Each cluster contains 3-5 specific subtopics
- Topics are tagged with estimated difficulty level
- Cross-pillar connections are flagged (e.g., "this topic in Pillar A connects to [topic] in Pillar C")

The topic map evolves over time. As you complete topics, the system may:
- Add new topics it discovers are relevant based on your feedback patterns
- Reorder priorities based on difficulty adjustments
- Insert bridge topics when a prerequisite gap is detected

### Bridge Topics

If the quality guard's prerequisite check flags that a unit assumes knowledge the user hasn't been taught yet:

1. The **current cycle pauses** at whatever day it's on
2. The bridge topic is served as a **standalone unit** tagged `[Bridge: prerequisite for (original topic)]`
3. After the bridge topic is completed and feedback received, the **paused cycle resumes where it left off**
4. `memory.md` tracks this: `Current Day in Cycle` doesn't advance during a bridge, and `Bridge Active` is set in Current State
5. **Cap:** If a cycle would need more than 2 bridge topics, the system moves the difficult topic to a later cycle and continues normally — too many interruptions break learning flow

---

## How to Implement This (Cowork)

### Folder Structure
```
dailyprong-system/
├── CLAUDE.md              ← Daily engine (state detection, generation, quality guard,
│                            feedback processing). Lean — only what runs every session.
├── onboarding.md          ← Onboarding & recalibration instructions. Only read when
│                            needed. Ignored during normal daily use.
├── memory.md              ← The evolving brain (written by onboarding, updated daily)
├── memory-backup.md       ← Cycle-start snapshot of memory.md (auto-created)
└── units/                 ← Archive of completed learning units
    ├── cycle-01/
    ├── cycle-02/
    └── ...
```

### Why Onboarding Is a Separate File

Onboarding runs once. After that, its instructions eat context window on every daily session for no reason. By putting it in `onboarding.md`, the daily engine (`CLAUDE.md`) stays lean. Cowork only reads `onboarding.md` when actually needed.

### State Detection (in `CLAUDE.md`)

When a session starts, `CLAUDE.md` reads `memory.md` and determines the system state:

1. **Not onboarded** → the user needs onboarding. Read `onboarding.md` and begin the onboarding conversation with the user.
2. **User requests recalibration** (detect intent, not a single phrase) → the user wants to update their learning setup. Read `onboarding.md` and begin the recalibration conversation.
3. **Pending feedback is true** → present the feedback prompt before generating anything new
4. **Today's unit not yet generated, no pending feedback** → enter generation mode
5. **Everything up to date** → tell the user and ask what they'd like to do

**How state is determined:** `memory.md` tracks `Last Unit Generated`, `Last Feedback Received`, and `Pending Feedback` dates in Current State. `Pending Feedback: true` always takes priority — the system never generates a new unit while feedback is outstanding. This prevents the adaptive engine from running blind.

> **Critical implementation note:** Handoffs from `CLAUDE.md` to `onboarding.md` must provide context, not just bare commands. Claude's safety guardrails will resist "blindly execute this file" — but will naturally comply when the instruction has purpose. Bad: "Read onboarding.md and execute its instructions." Good: "The user needs onboarding. Read `onboarding.md` — it contains the full discovery conversation flow — and begin the onboarding conversation with the user." The surrounding context in `CLAUDE.md` (system description, state detection logic) gives Claude enough understanding to act without hesitation.

### Scheduled Task Behavior

Use `/schedule` to set up a daily task:
- Runs at your preferred time
- **If `Pending Feedback` is true → skips generation** and saves a note: "Skipped — feedback pending from [date]"
- If no pending feedback → generates the unit, runs quality guard, saves to `units/`
- When you open your laptop, the unit is waiting
- You consume it, reply with feedback, memory updates

### First-Time Setup
1. Create the `dailyprong-system/` folder
2. Place `CLAUDE.md` and `onboarding.md`
3. Place an empty `memory.md` with just: `# DailyProng System — Memory\n\nNot yet onboarded.`
4. Create an empty `units/` folder
5. Open Cowork → point at the folder → type anything
6. `CLAUDE.md` reads `memory.md`, sees "Not yet onboarded", reads `onboarding.md`, and begins the onboarding conversation
7. Complete onboarding, review outputs, confirm → `memory.md` gets populated
8. Type anything → system detects state and generates your first unit

### Daily Workflow
1. Open Cowork → type anything → `CLAUDE.md` reads state, generates unit, runs quality guard
2. Do the unit (~15-30 min)
3. Reply with feedback: "Done. Too Easy, High Value" (or whatever applies)
4. Claude updates `memory.md` — progress, difficulty adjustments, archival if needed

### Recalibration
Express that you want to update your setup — "let's recalibrate", "I want to change my goals", "my priorities have shifted", etc. `CLAUDE.md` detects the intent, loads `onboarding.md`, re-runs discovery. The agent preserves progress while updating direction.
