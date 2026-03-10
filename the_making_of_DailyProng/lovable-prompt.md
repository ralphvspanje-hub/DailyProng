# Lovable Prompt: DailyProng Learning System

Build a private, single-user daily learning web application called "DailyProng." It's a goal-driven learning engine that generates personalized daily learning units using the Anthropic Claude API. The app replaces a markdown-file-based Cowork setup with a proper UI.

## Core Concept

The user goes through onboarding once, where the system discovers their career goals, skills, and learning preferences through a conversation with Claude. From that, it generates Strategic Pillars (3-6 knowledge domains), Career Phases with weighted emphasis per pillar, and a Topic Map. Then every day, the user gets one focused learning unit (~15-30 min) that's connected to a strategic theme, calibrated to their difficulty level, and quality-checked before delivery.

## Tech Stack

- Frontend: React + TypeScript + Tailwind
- Backend: Supabase (auth, database, storage)
- AI: Anthropic Claude API (claude-sonnet-4-20250514)
- Auth: Supabase auth (single user, email/password is fine)

## Database Schema (Supabase)

### `user_profile`
- id, name, current_role, target_role, long_term_ambition, daily_time_commitment, learning_cadence, cycle_length, learning_style, unique_differentiator, created_at, updated_at

### `pillars`
- id, user_id, name, description, why_it_matters, starting_level (1-5), current_level (1-5), trend (up/stable/down), last_difficulty_signal (too_easy/about_right/too_hard/null), phase_weight (percentage), sort_order, is_active, created_at

### `phases`
- id, user_id, name, timeline_start, timeline_end, goal, is_active, sort_order

### `phase_weights`
- id, phase_id, pillar_id, weight (percentage)

### `topic_map`
- id, pillar_id, cluster_name, subtopics (text array), difficulty_level, status (covered/queued/in_progress), priority_order, cross_pillar_connections (text)

### `cycles`
- id, user_id, cycle_number, pillar_id, theme (topic cluster name), status (active/completed/skipped), bridge_count, started_at, completed_at

### `units`
- id, cycle_id, pillar_id, section_number, section_type (concept/deep_dive/case_study/hands_on/synthesis), topic, difficulty_level, content (full unit markdown), is_bridge, is_bonus, bridge_prerequisite_for (nullable), feedback_difficulty (too_easy/about_right/too_hard/null), feedback_value (high/medium/low/null), feedback_note (text, nullable), is_pending_feedback, file_path_equivalent, created_at, feedback_given_at

### `progress_archive`
- id, cycle_id, summary (one-line compressed summary), avg_difficulty, avg_value, level_change

### `personal_notes`
- id, user_id, note, created_at (max 10, oldest deleted when exceeded)

### `onboarding_conversations`
- id, user_id, messages (jsonb array of role/content pairs), status (in_progress/completed), created_at

## Pages & UI

### 1. Onboarding Page (`/onboarding`)
- Chat-style interface
- System conducts discovery conversation across 5 dimensions (career trajectory, skills, industry context, learning style, differentiators)
- Target: 4-6 turns of back-and-forth
- After discovery, presents three outputs in a clean card layout:
  - **Pillars** — each as a card with name, rationale, starting difficulty, key topics
  - **Phase Map** — timeline visualization with weighted emphasis bars per pillar
  - **Topic Map** — expandable tree per pillar showing clusters and subtopics
- Review loop: user can adjust any output, system revises
- On confirm: write everything to database, redirect to dashboard
- The conversation with Claude should use web search (tools) when generating the topic map

### 2. Dashboard (`/dashboard`) — The Daily Hub
This is the main page the user sees every day. State-aware — shows different content based on system state:

**State: Pending Feedback**
- Shows the last generated unit with feedback buttons prominently displayed
- Three-button row for Difficulty: Too Easy / About Right / Too Hard
- Three-button row for Value: High / Medium / Low
- Optional text input for notes
- Submit button

**State: Ready for New Unit**
- If starting a new cycle: show **Pillar Selection Menu**
  - Cards for each active pillar, ranked by recommendation
  - Each card shows: pillar name, current level (visual 1-5 dots or bar), one-line recommendation reason
  - Starvation-flagged pillars get a subtle badge: "Haven't covered in X cycles"
  - Click a card to select → system generates the unit
- If mid-cycle: automatically generates next section
- Loading state while Claude generates (with web search enrichment)
- Unit displayed in a clean reading layout (see Unit Display below)

**State: Everything Up To Date**
- Action menu with cards/buttons:
  - "Next Section" → generate next section in cycle
  - "Bonus Unit" → different angle on current topic (max 1 per session)
  - "Review Past Units" → link to history
  - "See Progress" → link to progress page
  - "Skip This Pillar" → end current cycle, go to pillar selection
  - "Recalibrate" → go to onboarding page in recalibration mode

### 3. Unit Display Component
Used on dashboard when a unit is generated or presented. Clean reading layout:

- **Header bar**: Section X of Y | Cycle Theme: [theme] | Pillar: [pillar name]
- **Meta row**: Phase name | Difficulty Level visual (e.g., 3/5 dots) | Quality Guard badges (Freshness ✓ | Prerequisites ✓)
- If bridge: prominent tag "[Bridge: prerequisite for X]"
- **Sections** rendered from markdown:
  - Today's Focus (highlighted/bold)
  - Why This Matters For You
  - The Core Idea
  - Go Deeper (collapsible, shows search term with copy button)
  - Quick Exercise
  - Connection Point
- **Feedback bar** pinned at bottom (appears after user scrolls through or clicks "I've completed this")

### 4. Progress Page (`/progress`)
- **Pillar overview**: cards showing each pillar with current level, trend arrow, total sections completed, current phase weight
- **Cycle history**: list of past cycles with theme, pillar, status, average feedback
- **Difficulty chart**: line chart per pillar showing level over time (x = cycle, y = level 1-5)
- **Streak/consistency**: simple counter of how many cycles completed
- **Phase timeline**: visual bar showing current phase position and upcoming transitions

### 5. History Page (`/history`)
- Browse past units organized by cycle
- Filter by pillar, section type, difficulty level
- Search by topic
- Click to expand and re-read any unit
- Show feedback given for each unit

### 6. Settings Page (`/settings`)
- Edit profile (time commitment, cadence, cycle length)
- Manage pillars (add new via mini-discovery, delete with archive to old_knowledge)
- Manage phases (edit timelines, adjust weights, trigger phase transition)
- View/edit personal notes (max 10)
- Danger zone: full recalibration, reset system

## Claude API Integration

### System Prompt Construction
Instead of a CLAUDE.md file, the system prompt is constructed dynamically from the database state. Build a function `buildSystemPrompt()` that:

1. Reads user profile, active pillars, current phase + weights, recent progress, topic map, feedback patterns
2. Constructs a system prompt that includes:
   - Role description ("You are the DailyProng learning system...")
   - Current state context (active cycle, section number, pillar, theme, difficulty level)
   - Generation instructions per difficulty level (1-5 qualitative guides)
   - Section type instructions (what Concept vs Deep Dive vs Case Study etc. should contain)
   - Go Deeper resource type mapping (Concept → YouTube search term, Deep Dive → article/paper, Case Study → conference talk/podcast, Hands-On → repo/tutorial, Synthesis → nothing)
   - Quality guard instructions (check freshness against topics covered, check prerequisites)
   - Feedback patterns for this pillar
   - Recent progress log (last 3 cycles)
3. Sends this as the system prompt with the user message being the generation trigger

### API Calls
- Use `claude-sonnet-4-20250514` model
- Enable web search tool for unit generation (always) and onboarding (always)
- For onboarding: maintain conversation history in `onboarding_conversations` table, send full history each turn
- For unit generation: single-shot call with rich system prompt, no multi-turn needed
- For bonus units: include the current unit's content in the prompt so Claude knows what angle to take

### Key API Patterns
```typescript
// Unit generation
const response = await anthropic.messages.create({
  model: "claude-sonnet-4-20250514",
  max_tokens: 4000,
  system: buildSystemPrompt(userState),
  tools: [{ type: "web_search_20250305", name: "web_search" }],
  messages: [{ role: "user", content: "Generate today's learning unit." }]
});

// Onboarding conversation
const response = await anthropic.messages.create({
  model: "claude-sonnet-4-20250514",
  max_tokens: 2000,
  system: onboardingSystemPrompt,
  tools: [{ type: "web_search_20250305", name: "web_search" }],
  messages: conversationHistory
});
```

## State Machine Logic

The app's core logic is a state machine. On dashboard load:

```
1. Check: has user completed onboarding? 
   → No: redirect to /onboarding

2. Check: is there a unit with pending feedback?
   → Yes: show that unit with feedback UI

3. Check: is there an active cycle with remaining sections?
   → Yes: show "generating next section..." and call Claude API
   → No: show pillar selection menu for new cycle

4. After feedback submitted:
   → Process difficulty adjustment (two-consecutive-signals rule)
   → If last section of cycle: run cycle-end logic (archive, backup, advance)
   → Show "everything up to date" menu
```

### Two-Consecutive-Signals Rule (implement in code)
```typescript
function processDifficultyFeedback(pillar, newSignal) {
  if (newSignal === 'about_right') {
    pillar.last_difficulty_signal = null; // reset streak
    return;
  }
  if (newSignal === 'too_hard' && feedbackValue === 'low') {
    // Immediate action: park topic, offer pillar switch
    return handleParkAndSwitch(pillar);
  }
  if (pillar.last_difficulty_signal === newSignal) {
    // Two consecutive same-direction signals → change level
    if (newSignal === 'too_easy' && pillar.current_level < 5) pillar.current_level++;
    if (newSignal === 'too_hard' && pillar.current_level > 1) pillar.current_level--;
    pillar.last_difficulty_signal = null; // reset after change
  } else {
    pillar.last_difficulty_signal = newSignal; // store first signal
  }
}
```

### Cycle-End Logic
When feedback is given for the last section of a cycle:
1. Compress oldest detailed cycle to one-line summary in progress_archive
2. Increment cycle number
3. Clear active cycle's pillar and theme
4. Reset bridge count
5. Regenerate Topics Queued Next (3-5 entries from topic map)
6. Check phase date — if past end date, prompt user about phase transition
7. Check if any pillar hasn't appeared in last N cycles (starvation prevention)

## Design Direction

- **Dark mode by default** (with light mode toggle)
- Clean, minimal, focus on readability for the learning content
- Card-based UI for selections and overviews
- Subtle progress indicators (not gamified — no points, no badges, no streaks prominently displayed)
- The unit reading experience should feel like reading a well-formatted article, not a chat message
- Responsive — works on desktop and mobile (tablet too for reading units on the go)
- Typography-focused: good font pairing, generous line height for reading comfort

## What NOT to Build (Keep Simple)

- No social features, no sharing
- No notification system (the user comes to the app when ready)
- No spaced repetition or flashcard system
- No AI chat interface outside of onboarding — daily interaction is structured, not conversational
- No export functionality (maybe later)
- No multiple users (single user, single account)

## Implementation Order

1. Database schema + auth
2. Onboarding chat interface + Claude API integration
3. Dashboard state machine + unit generation
4. Unit display component
5. Feedback processing + difficulty adjustment logic
6. Pillar selection menu + cycle management
7. Progress page + history page
8. Settings + pillar management
9. Polish: animations, loading states, error handling
