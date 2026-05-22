# Streak: Habit Tracker

A single-page habit tracker with weekly streaks. Add daily habits, tick them off on a weekly grid, and watch your streaks grow.

## How to run

**Option 1: just open the file (no server needed)**

```bash
open index.html      # macOS
xdg-open index.html  # Linux
# or double-click index.html in your file explorer
```

The app is a single self-contained HTML file with no build step, no dependencies, and no server required.

**Option 2: serve locally (avoids any browser file:// quirks)**

```bash
# Python 3
python3 -m http.server 3000

# Node (npx, no install needed)
npx serve .

# Then open http://localhost:3000
```

**Requirements:** A modern browser (Chrome, Firefox, Safari, Edge — anything from 2022+). No Node, no npm, no build tools.

## Stack

Vanilla HTML + CSS + JavaScript. Single file, zero dependencies, zero build step.

## Features

- Add, rename, and delete habits
- Weekly grid: habits on the left, Mon–Sun across the top
- Today's column highlighted in amber
- Checkmarks persist across reloads (localStorage)
- Week navigation: previous/next week, "this week" shortcut
- Past weeks show historical checkmarks (read-only)
- Future days are disabled
- Streak counter per habit (consecutive days up to today)
- Streaks ≥ 7 days turn green
- Responsive down to 360px
- Dark mode (auto, follows OS)
- Keyboard navigation (arrows to move through the grid, Enter/Space to tick)
- Empty state when no habits added

## File structure

```
habit-tracker/
├── index.html    # entire app — HTML, CSS, JS all inline
├── README.md
└── ANSWERS.md
```
