# Lovable Prompt — Mentor Page UI Redesign

Redesign the Mentor page (/mentor) UI only. Do not change any logic, API calls, data, or other pages.

---

## Goal

The Mentor page should feel like Claude.ai or Gemini — a clean, centered chat interface that transitions smoothly into a full conversation view once the user sends their first message.

---

## Empty State (before first message is sent)

### Layout

The page has two phases: empty state and active state. On load, show the empty state.

The vertical layout of the empty state from top to bottom:

1. The mentor's opening message displayed as a chat bubble (assistant side) — this is already in the app, keep it as-is. Position it in the upper-center area of the page with comfortable padding.
2. A large centered input box (see Input Box spec below).
3. Quick-action buttons displayed as a row of pill-shaped buttons directly below the input box.

The input box and quick-action buttons should be vertically centered on the page as a group, sitting roughly in the middle of the viewport. The opening mentor message floats above this group.

### Input Box (empty state)

The input box should be large and inviting — similar in scale to Claude or Gemini's home input:

- Minimum height: 80px, expands vertically as the user types (auto-grow up to ~200px, then scrolls)
- Width: centered, max-width 680px, with horizontal padding on smaller screens
- Rounded corners (large border-radius, like Claude/Gemini)
- Subtle border with a slight glow or highlight on focus
- Placeholder text: "Ask [mentor name] anything..."
- The send button lives inside the input box, bottom-right corner — an arrow icon (like Claude) or paper plane icon. It is disabled and visually muted when the input is empty, becomes active and highlighted (orange accent to match the app theme) when there is text.
- No external send button outside the box.

### Quick-Action Buttons (empty state)

Display directly below the input box as a horizontal row of pill-shaped buttons:

- Add a Pillar
- Swap a Pillar
- Edit a Pillar
- Change Level
- Delete a Pillar
- Full Recalibration

Style: pill shape (fully rounded), small-to-medium size, subtle background (slightly lighter than page background), text in muted color. On hover: slight background lift and text brightens. Same style as Claude's suggestion chips or Gemini's quick-start buttons.

On click: immediately sends the corresponding trigger message and transitions to active state.

---

## Transition: Empty State → Active State

When the user sends their first message (either by typing and pressing send, or clicking a quick-action button):

1. Animate the input box sliding down to the bottom of the viewport. Use a smooth ease-in-out transition (~300ms). The input box ends up pinned to the bottom of the screen.
2. The conversation area fades or slides in above the pinned input box, starting from where the input was and expanding upward.
3. The quick-action buttons in the empty state disappear. In the active state, the quick-action buttons reappear as a compact row directly above the pinned input box (smaller pills, same labels).
4. The opening mentor message and the user's first message are now visible in the conversation area.

This transition should feel fluid and intentional — not jarring. Similar to how Claude.ai animates from its home state into a conversation.

---

## Active State (conversation in progress)

### Conversation Area

- Scrollable, fills the space above the pinned input bar
- User messages: aligned right, accent-colored bubble (use the app's orange or a warm dark tone)
- Assistant messages: aligned left, slightly lighter background bubble
- Comfortable padding and line height for readability
- Auto-scrolls to the latest message

### Pinned Input Bar (bottom)

- Full width, fixed to the bottom of the viewport
- Input box same style as empty state but slightly more compact vertically (min-height ~56px)
- Send button inside the box, bottom-right
- Quick-action pill buttons in a row directly above the input box (smaller than empty state version)
- Background behind the pinned bar has a subtle fade/blur so conversation content beneath it doesn't clash

---

## Behavior Notes

- If the page is loaded and there are existing messages in mentor_conversations, skip the empty state entirely and go straight to active state with conversation history loaded.
- The input box auto-focuses on page load in the empty state.
- Pressing Enter sends the message (Shift+Enter for new line).
- Character counter (0/2000) shown subtly inside the input box near the send button.
