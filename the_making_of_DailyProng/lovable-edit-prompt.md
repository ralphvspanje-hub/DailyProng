# Lovable Edit Prompt — DailyProng Updates

Make the following changes to the existing DailyProng app. Do not rebuild from scratch — these are targeted additions and edits to what already exists.

---

## 0. Security Requirements

Implement these first — they affect all AI features.

**All Anthropic API calls must happen exclusively in Supabase Edge Functions.** Never call the Anthropic API from frontend code. The API key is stored as a Supabase secret and is never exposed to the browser. Every Edge Function must verify the user's Supabase auth JWT before processing any request — reject unauthenticated calls with 401.

**Rate limiting.** Create a table called `api_rate_limits` with columns: id, user_id, ip_address, endpoint (text), call_count (integer), window_start (timestamptz). In every Edge Function that calls the Anthropic API, check this table before proceeding:

- If the user has made more than 50 calls today (rolling 24h window), return 429 with message "Daily limit reached."
- If the IP address has made more than 100 calls today, return 429 Too Many Requests.
- Otherwise increment the counter and proceed.
- Clean up rows older than 48 hours periodically.

**Input length limits.** Before any user text is sent to an Edge Function or saved to the database, enforce these limits on the frontend: mentor messages max 2000 characters, onboarding messages max 3000 characters, feedback notes max 500 characters. Show a character counter and disable send if over limit.

**Prompt injection protection.** When including user-provided text in any Claude API system prompt or message, always wrap it in clear delimiters so Claude can distinguish user content from system instructions. Wrap mentor chat messages as: [USER MESSAGE]: {content} [/USER MESSAGE]

**Markdown rendering safety.** When rendering AI-generated unit content as markdown, use react-markdown with default settings (which strip dangerous HTML). Do not use dangerouslySetInnerHTML anywhere for AI-generated content.

---

## 1. Rename Everywhere

Replace every instance of the name "Powerhouse" with "DailyProng" — nav bar, page titles, meta tags, copy, auth pages, loading screens, everywhere.

---

## 2. Fix Model Name

In all Claude API calls throughout the codebase, replace any outdated model string (including claude-sonnet-4-20250514 or any similar variant) with `claude-sonnet-4-6`.

---

## 3. New Database Table: mentor_conversations

Add this table to Supabase:

- id (uuid, primary key)
- user_id (uuid, references user_profile)
- role (text — "user" or "assistant")
- content (text)
- created_at (timestamptz, default now())

---

## 4. Mentor Name Setting

Add a `mentor_name` field (text, nullable) to the `user_profile` table.

Throughout the entire app, wherever the mentor is referenced by name — nav label, page titles, buttons, empty state text, system prompts — use the value of `mentor_name` from the user's profile. If `mentor_name` is null or empty, fall back to "Mentor" as the default display name.

This means every hardcoded reference to "Mentor" in the UI must be dynamically replaced with the user's chosen name at runtime.

---

## 5. New Page: Mentor (/mentor)

Add "Mentor" (or the user's custom mentor name) as the 5th nav item after Settings. Full page at /mentor.

### Empty State Layout

When no conversation has started yet, show a centered hero block in the middle of the page.

Title: Your [mentor name]

Body: Not just a learning engine — a thinking partner. Ask anything about your career direction, goals, or learning path. [Mentor name] knows your pillars, your progress, and your trajectory. They ask before they act.

Below the hero, show a row of quick-action buttons:

- Add a Pillar
- Swap a Pillar
- Edit a Pillar
- Change Pillar Level
- Delete a Pillar
- Full Recalibration

When the user clicks one of these buttons, it immediately sends a trigger message that starts the right AI conversation:

- Add a Pillar → sends: "I want to add a new pillar to my learning plan."
- Swap a Pillar → sends: "I want to swap out one of my existing pillars for a new one."
- Edit a Pillar → sends: "I want to edit one of my existing pillars."
- Change Pillar Level → sends: "I want to reassess the difficulty level for one of my pillars. Can you ask me some questions to figure out the right level?"
- Delete a Pillar → sends: "I want to delete one of my pillars."
- Full Recalibration → sends: "I want to do a full recalibration of my entire learning plan and career goals."

### Active Conversation Layout

Standard chat layout: messages top to bottom (user right, assistant left). Once a conversation starts, the quick-action buttons move to a compact row above the input bar so they are always accessible. Text input bar pinned to bottom with send button. Conversation persists across sessions — load from mentor_conversations table on page load.

### Mentor AI — System Prompt

Build the system prompt dynamically from the database on every message. The system prompt must include:

- User profile: name, current_role, target_role, long_term_ambition
- Active pillars: name, current_level, phase_weight, trend for each
- Current phase: name, timeline, goal
- Recent progress: last 3 cycles from progress_archive or cycles table
- The mentor's name as chosen by the user (or "Mentor" if not set)

Mentor behavior instructions to include in the system prompt:

You are [mentor name], the DailyProng career mentor — a sharp, thoughtful thinking partner focused on the user's long-term career growth. You ask before you act. Never change pillars or settings without explicit user confirmation. When the user wants to add, swap, edit, or delete pillars: ask clarifying questions first (2–4 turns), then summarize proposed changes clearly, then wait for confirmation. When the user wants to change a pillar level: ask 3–5 diagnostic questions about their experience with the topic to assess their actual level, then recommend a specific level with reasoning. When discussing career direction: be honest, curious, and specific. Reference their actual goals and pillars. Keep responses focused and actionable. No filler. You are not a general chatbot — stay within career growth, learning strategy, and the user's DailyProng setup. When you propose pillar changes, always end your confirmation message with a PROPOSED_CHANGES block so the app can parse and apply it.

### Proposed Changes Format

When the mentor proposes changes to pillars, the assistant message must end with a PROPOSED_CHANGES block followed by a JSON object. The JSON must have two fields: "action" and "changes".

Action must be one of: add_pillar, delete_pillar, edit_pillar, swap_pillar, change_level, full_recalibration

Changes field shape per action:

- add_pillar: name, description, why_it_matters, starting_level, phase_weight, topic_clusters (array of objects with cluster_name and subtopics array)
- delete_pillar: pillar_id
- edit_pillar: pillar_id, fields_to_update (object with pillar fields to update)
- swap_pillar: remove_pillar_id, add (same shape as add_pillar)
- change_level: pillar_id, new_level
- full_recalibration: redirect (boolean true)

### Applying Proposed Changes

When an assistant message contains a PROPOSED_CHANGES block:

1. Parse the JSON
2. Show a confirmation card directly below that message with title "Apply these changes?", a human-readable bullet summary of what will change, and two buttons: Confirm and Cancel
3. On Confirm: apply changes to Supabase (update pillars, phase_weights, topic_map as needed), show a loading spinner, on success show a toast "Changes applied" and refresh all data by invalidating queries so Progress, Settings, and Today all reflect the update. If action is full_recalibration, redirect to /onboarding.
4. On Cancel: dismiss the card and continue the conversation normally.

### Context Window

Load the last 30 messages from mentor_conversations for this user and include them as conversation history in every API call. This is how the mentor remembers previous conversations.

---

## 6. Settings Page — Pillar Management and Mentor Name

### Mentor Name Setting

Add a new section at the top of the Settings page called "Your Mentor". It contains a single text input with label "Mentor name" and placeholder "Mentor". Below the input, show hint text: "This is what your AI mentor will be called throughout the app. Leave blank to use the default." Save button updates the mentor_name field in user_profile. The name updates everywhere in the app immediately on save.

### Pillar Management

In the Pillars section of Settings, add two simple direct actions per pillar that require no AI conversation:

**Change Level** — a small number input or +/− stepper (range 1–5) next to each pillar. On save, updates current_level directly in Supabase.

**Delete** — a delete button per pillar. Shows a confirmation dialog: "Delete [Pillar Name]? This cannot be undone." On confirm, removes the pillar from the pillars, phase_weights, and topic_map tables.

For adding, swapping, or editing pillars: remove those controls from Settings if they exist. Add a small note in the Pillars section: "To add, swap, or edit pillars, talk to your [mentor name]."

Rename the Danger Zone action from "Full Calibration" or "Full Recalibration" to "Rebuild Your Learning Plan" with subtext: "Restart the onboarding conversation to redefine your career goals, pillars, and topic map from scratch. Your history is preserved."

---

## 7. "You're Up To Date" Screen — Redesign

This is the screen shown on the Today page after feedback has been submitted and the user is caught up. Redesign it as follows.

Remove the "See Progress" and "Review Past Units" action cards entirely from this screen. They remain accessible via the nav bar as before — just not shown here.

Replace the "Recalibrate" card with a "Talk to [mentor name]" button that navigates to /mentor.

New layout:

- One large primary button centered on screen: "Next Section" with subtitle "Continue current cycle". This should be visually prominent — larger than the other buttons, full width or nearly so.
- Below it, two smaller side-by-side buttons: "Bonus Unit" (different angle on current topic) and "Talk to [mentor name]" (navigates to /mentor).

The "Talk to [mentor name]" button label must use the user's chosen mentor name dynamically, falling back to "Talk to Mentor" if no name is set.

---

## 8. New About Page (/about)

Add a route at /about. Add a small "About DailyProng" link in the footer on every page that links to this route.

### Page Content

Heading: What is a Prong?

Body:

Most career advice still talks about T-shaped people — deep in one domain, broad across others. Marc Andreessen and others argued that T-shaped was the model for a more stable era.

Today, jobs merge. Domains collapse into each other. The engineer needs product sense. The product manager needs to understand systems. The leader needs to think like a technologist.

The new shape isn't T. It's E. Or F. Or a fork — multiple deep tines growing from a shared foundation.

A Prong.

DailyProng is built on that idea. Not one deep skill. Not shallow breadth. A deliberate set of growing tines — each one strategic, each one connected, each one built through focused daily practice.

One section per session. One cycle at a time. Compounding over months into something that actually changes what you can do.

You don't need to know everything. You need to know the right things — deeply — and keep adding tines.

Footer line on this page: Built by Ralph van Spanje

Design: same dark theme as the rest of the app, clean typography, generous line height, no images, let the text breathe.
