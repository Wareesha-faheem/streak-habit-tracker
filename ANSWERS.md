# ANSWERS.md

## 1. How to run

The app is a single HTML file with no build step or dependencies.

**Simplest:**
```bash
open index.html   # macOS, just double-click, or:
```

**With a local server (recommended to avoid `file://` quirks):**
```bash
python3 -m http.server 3000
# then visit http://localhost:3000
```

Or with Node: `npx serve .`

Requirements: any modern browser. Nothing to install.

---

## 2. Stack & design choices

**Stack:** Vanilla HTML + CSS + JavaScript. The habit tracker's entire feature set (state, DOM, events, persistence) fits cleanly in ~350 lines of JS without React or Vue overhead. A single `.html` file is also the most robust deliverable: zero dependency rot, zero build failures, trivially hostable anywhere.

**Visual decision 1: the fixed-column monospace grid.**
The seven day columns use a fixed pixel width (`52px` desktop, `38px` mobile) rather than a fractional grid. The habit name column takes all remaining space with `minmax(0, 1fr)`. This means the grid cells are always the same size regardless of habit name length, creating the visual rhythm of a physical tracking log. The alternative, letting columns flex, would have made the checkmark cells balloon on short names and shrink on long ones, destroying the scannability that is the entire point of a grid layout. The tradeoff is that very long names truncate; I use `text-overflow: ellipsis` with `title` tooltip to preserve access.

**Visual decision 2: amber as the single accent color, with two semantic states.**
Almost everything in the UI is off-white, charcoal, and light grey. Amber (`#C17B2F` / `#E8A84A` dark mode) appears in exactly three places: today's column highlight, the checked state of a checkbox, and the streak pill when active. This restraint means amber carries genuine signal,when you see it, something meaningful is happening. Streaks of 7+ days escalate to green, giving a visual "leveling up" moment without adding a third color. The choice to use amber (not blue, not green) was intentional: it reads as warm and habitual rather than cold and transactional.

**Week starts Monday.** Most habit trackers are about work-week behavior, exercise before work, reading in the evening, etc. Monday framing keeps the weekend together on the right as a natural visual block, which is both more honest about human routine and easier to read at a glance. A Sunday start splits the weekend across both edges of the grid, which is visually noisy for no user benefit.

**Streak counting:** The streak counts consecutive days up to and including today, *or* up to yesterday if today is unchecked. Rationale: if you've checked every day for two weeks and it's 9am on Monday, you haven't broken your streak, you just haven't had a chance to check yet. Requiring today's check to maintain the count would penalize morning users and create anxiety rather than motivation. The streak drops to 0 only if yesterday is also unchecked, meaning an actual gap exists.

---

## 3. Responsive & accessibility

**Responsive behavior:**

At 1440px: the grid breathes. Habit names have generous space, columns are 52px wide, streak pills are readable. The header is sticky so week navigation stays accessible on long lists.

At 360px: the grid template drops to `32px` columns, checkboxes shrink to 20×20px, the "This week" button hides (week navigation arrows remain), and the flame icon inside the streak pill disappears (only the number shows). The habit name column still uses `minmax(0, 1fr)` so it fills remaining space without overflow. The layout never breaks, it degrades gracefully at every breakpoint from 1440px down to 320px.

**Accessibility: what I handled:**

- *Keyboard navigation:* Arrow keys move focus through the checkmark grid (left/right/up/down). Enter or Space toggles the focused cell. Tab moves through focusable elements in document order. All interactive elements have visible `:focus-visible` outlines in amber.
- *ARIA labels:* Every checkmark button has an `aria-label` describing the habit name and the full date (`aria-label="Exercise on Monday, May 19, 2025"`). The streak pills have `aria-label="N day streak"`. Column headers use `role="columnheader"` and descriptive `aria-label`. The week label uses `aria-live="polite"` so screen readers announce week changes.
- *`aria-pressed`:* Checkmark buttons use `aria-pressed` (true/false) to communicate toggle state without relying on visual color alone.
- *Color contrast:* Amber text on amber-tinted background (the streak pill and today column) was checked at 4.5:1+. The ink-on-surface text contrast exceeds 7:1.

**Accessibility: what I knowingly skipped:**

I did not build a full live-region announcement for checkmark toggles (e.g. "Exercise checked for Monday"). I judged that announcing every grid cell toggle would be noisy for screen reader users and that `aria-pressed` state is sufficient for understanding toggle state without a running commentary. With another day I would test this with an actual screen reader (VoiceOver, NVDA) and add a polite announcement only when a streak milestone is hit (e.g. "7-day streak reached!"), which is the meaningful event.

---

## 4. AI usage

I used Claude for a small amount of assistance during development, mainly for brainstorming parts of the UI structure and some CSS styling ideas for the habit tracker layout. The AI suggested an initial version of the grid layout and streak section.

However, most of the project was implemented and refined manually by me, including the localStorage logic, week navigation system, streak calculation, habit editing/deleting functionality, keyboard navigation, and responsive behavior.

One specific change I made to the AI-generated output was modifying the responsive grid sizing. The original AI suggestion used larger fixed checkbox columns, which caused overflow issues on smaller screens. I manually changed the grid column sizes inside the media queries (52px, 38px, 32px) and adjusted the checkbox/button dimensions so the tracker would fit properly on mobile devices without horizontal scrolling.

---

## 5. Honest gap

The weakest part of this submission is the **delete confirmation UI**. I used `window.confirm()` which is functional but jarring. It breaks the aesthetic completely: an ugly grey system dialog over a polished off-white UI. It also has no ability to undo.

With another day I would replace it with an inline confirmation pattern: clicking the delete button would turn the habit row red and show "Delete?" / "Cancel" inline within the row, disappearing after 4 seconds if not confirmed. This keeps the user in context, avoids the jarring modal, and gives an implicit undo window (just don't click "Delete"). I'd also add an undo toast after deletion so accidental deletions are recoverable without data loss.
