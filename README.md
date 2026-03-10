# DailyProng — Personal Learning Engine

**Your career growth on autopilot. One unit per day.**

![Claude Desktop](https://img.shields.io/badge/Claude_Desktop-Cowork-D97757?style=flat)
![Prompt Engineering](https://img.shields.io/badge/Prompt_Engineering-Agent_Workflow-8B5CF6?style=flat)

---

## The Problem

Online courses pile up. YouTube tutorials get half-watched. You bookmark articles you never revisit. Most self-directed learning fails not because of bad content, but because there's no system — no structure, no progression, no feedback loop.

You end up learning *around* your goals instead of *toward* them.

## The Solution

DailyProng turns Claude into a personal tutor that knows your career trajectory. It onboards you once, maps your knowledge gaps to strategic pillars, and generates focused 15–30 min learning units that build on each other. Adaptive difficulty, cycle-based progression, and strict quality guards keep every session relevant and challenging.

You open Cowork, type "go", and learn something that actually matters.

## Key Features

- **One-Time Onboarding** — A structured conversation discovers your career goals, current skills, and learning preferences. You review and adjust the proposed architecture before anything starts.
- **Daily Learning Units** — Each unit is tied to a strategic theme, calibrated to your level, and quality-checked for freshness and prerequisites before delivery.
- **Adaptive Difficulty** — Each knowledge pillar tracks its own difficulty level (1–5). Two consecutive "Too Easy" ratings bumps it up; two "Too Hard" brings it down.
- **Cycle-Based Progression** — Themed multi-section cycles move through Concept → Deep Dive → Case Study → Hands-On → Synthesis. Pick 5-section or 3-section compressed.
- **Strict Memory Management** — All state lives in a single file kept under ~3,000 tokens. The system stays fast even after months of daily use.
- **Built-In Commands** — Recalibrate your goals, request bonus units, view progress, skip pillars, or defer units to the next session.

## Architecture

**Two-file split.** `CLAUDE.md` is the daily engine, loaded every session. `onboarding.md` handles first-time setup and recalibration only. This keeps the daily context window lean and focused.

**Single state file.** Everything — your profile, pillars, phases, topic map, progress, and feedback patterns — lives in `memory.md`. Strict archival rules prevent bloat over time.

**Quality guard.** Every unit is checked against your history (no repeated topics) and your progression (no assumed knowledge you haven't covered yet).

**Feedback-driven.** Your two-word rating after each unit ("About Right, High") feeds back into the difficulty engine and topic selection for the next session.

```
New Session
     │
     ▼
┌──────────────┐     ┌───────────────────┐
│  CLAUDE.md   │────▶│  Read memory.md   │
│  (engine)    │     │  (state + profile) │
└──────────────┘     └───────────────────┘
     │                        │
     ▼                        ▼
┌──────────────────────────────────┐
│  Generate Learning Unit          │
│  Topic selection, web research,  │
│  quality check, difficulty cal.  │
└──────────────────────────────────┘
     │
     ▼
┌──────────────────────────────────┐
│  Deliver Unit + Collect Feedback │
│  Update memory.md               │
└──────────────────────────────────┘
     │
     ▼
┌──────────────────────────────────┐
│  units/                          │
│  Archived learning units         │
└──────────────────────────────────┘
```

## Repo Structure

```
DailyProng/
├── README.md                      ← You are here
├── the_making_of_DailyProng/      ← All prompts used to build this system
└── plugin/                        ← Point Cowork at this folder
    ├── CLAUDE.md                  ← Daily engine (loaded every session)
    ├── onboarding.md              ← Onboarding & recalibration
    ├── memory.md                  ← System state (the only file that changes)
    └── units/                     ← Archive of completed learning units
```

## How It Works

**1. Onboard** — Point Cowork at the `plugin/` folder and type anything. The system detects you're new and runs a ~15-30 min onboarding conversation to map your goals, skills, and preferences into a learning architecture.

**2. Learn** — Each session, type "go". A new unit is generated — researched, structured, and calibrated to your current level and cycle position.

**3. Grow** — Rate the unit ("About Right, High"). The system adjusts difficulty, logs progress, and queues the next topic. Come back tomorrow or keep going — it's session-based, not calendar-based.

## Quick Start

### Prerequisites

- [Claude Desktop](https://claude.ai/download) with Cowork enabled
- Claude Pro subscription
- Domain allowlist set to "All domains" in Claude Desktop settings

### Setup

1. Clone this repo or download the `plugin/` folder
2. Open Claude Desktop → switch to Cowork → point it at `plugin/`
3. Type anything — onboarding starts automatically
4. Complete onboarding → close the session → open a new one → type "go"

## Tips

- **Model choice:** Use Sonnet for both daily sessions and onboarding. Opus can cause timeout crashes on large memory writes.
- **Pacing is flexible:** Do all 5 cycle sections in one sitting or spread them across weeks.
- **If memory.md gets corrupted:** `memory-backup.md` is auto-created at each cycle start. Copy it back and you lose at most one cycle.

## The Making Of

Curious how this was built? The `the_making_of_DailyProng/` folder contains every prompt used to vibe-code this system from scratch — a behind-the-scenes look at building an agent workflow through conversation.

## License

MIT

---

<p align="center">
  Built by <a href="https://www.linkedin.com/in/ralphvanspanje"><strong>Ralph van Spanje</strong></a>
</p>
