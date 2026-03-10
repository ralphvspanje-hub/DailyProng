# DailyProng System — Execution Guide

A simple step-by-step guide to go from these files to a working daily learning system.

---

## What You Have

Five files — all go into the same folder:

| File | What It Is | When It's Used |
|------|-----------|----------------|
| `learning-framework-outline.md` | The full design of the system | Stages 1, 2, and 3 |
| `stage-1-exploration.md` | Prompt for the exploration conversation | Stage 1 only |
| `stage-2-create-plan.md` | Prompt for creating the implementation plan | Stage 2 only |
| `stage-3-implementation.md` | Prompt for the implementation agent | Stage 3 only |
| `execution-guide.md` | This file — your step-by-step checklist | Reference throughout |

---

## Step 0: Validate Platform Prerequisites

Before building anything, verify that Cowork can do what this system requires. This takes 10 minutes and can save you hours.

**Test 1: Conditional file reading**
1. Create a folder with two files:
   - `CLAUDE.md` containing: `You are a quiz system. Your quiz questions are stored in test.md. When the user starts a session, read test.md and begin the quiz with the user.`
   - `test.md` containing: `Ask the user 3 trivia questions, one at a time. Wait for each answer before asking the next. Question 1: What is the capital of Australia? Question 2: What year did the Berlin Wall fall? Question 3: What is the chemical symbol for gold?`
2. Open Cowork, point it at the folder, type "go"
3. **Pass:** Claude reads `test.md` and starts asking you trivia questions
4. **Fail:** Claude ignores `test.md` or refuses to read it → you'll need to inline onboarding logic into `CLAUDE.md` (see outline for fallback)

**Test 2: File write access**
1. In the same session, ask Claude to "create a file called `output.md` with the text 'hello world'"
2. **Pass:** The file appears in your folder
3. **Fail:** Cowork can't write files → the system won't work

**Test 3: Multiple sessions** (optional, needed for testing during build)
1. Open a second Cowork session pointed at the same folder
2. **Pass:** Both sessions work independently
3. **Fail:** You'll need to close the implementation session each time you test

If Tests 1 and 2 pass, proceed. If not, stop and troubleshoot before investing time in the full build.

---

## Step 1: Set Up the Folder

On your machine, create:

```
dailyprong-system/
├── learning-framework-outline.md
├── stage-1-exploration.md
├── stage-2-create-plan.md
├── stage-3-implementation.md
└── units/                           ← create empty folder
```

---

## Step 2: Start Exploration in Cowork

Open Claude Desktop. Switch to Cowork. Point it at the `dailyprong-system/` folder.

**Send one message:**
> "Read `stage-1-exploration.md` for your instructions and `learning-framework-outline.md` for the framework you need to explore. Begin."

---

## Step 3: Answer the Exploration Questions

The agent will come back with a structured summary of its understanding and a batch of questions grouped by topic.

**Your job:**
- Read its summary — correct anything it got wrong
- Answer every question honestly. Don't rush. The quality of this conversation directly determines the quality of the final system
- If you're unsure about something, say so — better to flag it now than have the agent assume
- If the agent flags something in the framework as suboptimal and suggests an improvement — take it seriously

**Keep going** until the agent says: *"Exploration complete. I'm ready for the plan stage."*

Before that, it will:
1. Run a completeness check against the full outline
2. Write an **Exploration Summary** document to the folder with all decisions consolidated

**Check that the Exploration Summary exists and looks complete before moving to Step 4.**

---

## Step 4: Create the Plan (Same Cowork Session)

Once exploration is complete, **in the same session**, send:

> "Read `stage-2-create-plan.md`. Create the implementation plan based on everything we discussed. Save it as `implementation-plan.md` in this folder."

The agent will produce the plan and write it directly to your folder.

**Your job:**
- Read the plan carefully
- Check that every decision from the exploration is captured in "Critical Decisions" and "Resolved Ambiguities"
- Check that `onboarding.md` is a separate step built before `CLAUDE.md`
- Check that `CLAUDE.md` is broken into mode-by-mode steps (not one big step)
- Check that each step has a verification method
- Check that the testing workaround (temporary `CLAUDE.md` for onboarding testing) is included
- Check that the pillar rotation algorithm, memory archival rules, and pending feedback gate are each covered by at least one step

**If anything is missing or wrong**, tell the agent. Iterate until the plan is solid.

---

## Step 5: Start a Fresh Cowork Session for Implementation

Close the exploration/plan session. This is intentional — the implementation agent needs a clean context window.

Open a new Cowork session. Point it at the same `dailyprong-system/` folder.

**Send one message:**
> "Read `stage-3-implementation.md` for your instructions, `implementation-plan.md` for the plan to execute, and `learning-framework-outline.md` for the framework design. Begin with Step 1."

---

## Step 6: Build and Test Each Piece

The agent will work through the plan step by step, waiting for your confirmation after each one.

**The build order will be roughly:**

1. **Folder structure + starter `memory.md`** — trivial, just confirm
2. **`onboarding.md` + temporary `CLAUDE.md`** — the agent writes `onboarding.md` plus a temp `CLAUDE.md` with context-rich handoff language (e.g., "You are the DailyProng learning system. The user needs onboarding. Read `onboarding.md` and begin the onboarding conversation.")
   - **You test it:** Open a **separate Cowork session**, point at the folder, type "go". Does onboarding start? Does the conversation flow well? Does it write to `memory.md` correctly?
   - Report back to the implementation agent what happened
3. **Real `CLAUDE.md` scaffold** (replaces the temp one) — state detection logic, memory backup trigger
   - **You test it:** Open Cowork with the populated `memory.md` from your onboarding test. Type "go". Does it correctly NOT re-trigger onboarding? Does it try to enter generation mode?
4. **Generation mode** → test by getting a daily unit. Check that pillar selection follows the rotation rules.
5. **Quality guard** → test by checking the freshness and prerequisite checks in the unit header
6. **Feedback processing** → test by replying "Done. About Right, High" and checking if `memory.md` updated correctly. If you've done enough units, check if the archival compression kicked in.
7. **Scheduled task mode** → test by setting up a scheduled task via `/schedule` (if available)

**Key rule:** Don't say "looks good" if you haven't actually tested it in Cowork. Open a separate session, try it, and report real results.

---

## Step 7: Run Your First Real Day

Once all modes are confirmed working:

1. Open Cowork → point at `dailyprong-system/`
2. Type "go"
3. Read today's unit, do the exercise
4. Reply with your feedback
5. Done

Tomorrow, do it again. The system compounds from here.

---

## Step 8: Clean Up the Folder

After everything is working, remove the setup files:

```
Delete from the folder:
- stage-1-exploration.md
- stage-2-create-plan.md
- stage-3-implementation.md
- execution-guide.md
- exploration-summary.md

Keep for reference (move to a subfolder like docs/):
- learning-framework-outline.md
- implementation-plan.md

Your clean daily folder:
dailyprong-system/
├── CLAUDE.md
├── onboarding.md
├── memory.md
├── memory-backup.md       ← auto-created at each cycle start
└── units/
```

---

## If Things Go Wrong

| Problem | What To Do |
|---------|-----------|
| Onboarding asks weird questions | Go back to the implementation agent, paste what happened, ask it to fix `onboarding.md` |
| Unit picks the wrong topic | Check `memory.md` — are the pillars, weights, pillar rotation history, and progress log correct? If yes, the generation logic in `CLAUDE.md` needs tweaking |
| Quality guard never flags anything | This is expected for freshness in the first few cycles (nothing to repeat yet). For prerequisites, ask for a unit on an advanced topic and see if it catches the gap. |
| Feedback doesn't update memory | Check if Cowork has write access to the folder. Then check the feedback parsing logic in `CLAUDE.md` |
| Difficulty level won't change | Remember the two-consecutive-signals rule — one "Too Easy" won't change the level. Give the same feedback twice for the same pillar and check again. |
| `memory.md` feels too long | Check if the archival rules are running: only the last 3 cycles should be in full detail. If older cycles aren't compressed, the archival logic in `CLAUDE.md` needs fixing. |
| `memory.md` gets corrupted or deleted | Check if `memory-backup.md` exists — copy it to `memory.md` (you lose at most one cycle). If not, the system can partially reconstruct from `units/` files, or run recalibration to start fresh. |
| You want to change your goals | Say "let's recalibrate" (or express the intent in your own words) — it loads `onboarding.md` and re-runs discovery |
| Cycle feels too long/short | Tell the system "Change my cadence to X days" — it updates `memory.md` accordingly |
| System generates while feedback is pending | This is a bug — the pending feedback gate should block generation. Report to implementation agent for fix. |

---

## Timeline Estimate

| Stage | Time | Where |
|-------|------|-------|
| Prerequisites check (Step 0) | 10 min | Cowork |
| Exploration (Steps 2-3) | 30-60 min | Cowork session 1 |
| Plan creation (Step 4) | 15-30 min | Same Cowork session |
| Implementation (Steps 5-6) | 2-4 hours | Cowork session 2 (fresh) |
| First real session (Step 7) | 15-30 min | Cowork session 3 (your daily driver) |
| **Total setup** | **~Half a day** | |
