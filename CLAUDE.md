# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A calm, mobile-friendly Pomodoro **body-doubling** focus timer, designed to be displayed on a monitor or iPad during TikTok Live sessions. The entire app is a single self-contained `index.html` (embedded CSS + vanilla JS), with no build step, no dependencies, and no network access required.

## Running / Testing

There is no toolchain. Open `index.html` directly in a browser (`file://` works), or serve it for testing on mobile:

```sh
python3 -m http.server 8000   # then open http://<lan-ip>:8000
```

Manual verification is the only test path. Target browsers: **iPhone Safari, iPad Safari, Chrome, desktop**. After changes, re-check: timer accuracy across screen sleep, audio after first tap, and `localStorage` persistence across reloads.

## Hard constraints (do not break)

These come from the product requirements and override convenience:

- **Single file only.** All HTML, CSS, and JS live in `index.html`. No external libraries, fonts, CDNs, or separate assets. No internet dependency at runtime.
- **Vanilla JS, ES5-safe style.** The code deliberately uses `var`, function declarations, and `Array.prototype.forEach.call` for maximum mobile-browser compatibility. Match that style; avoid features that may break in older mobile WebViews/previews.
- **Audio must start only after a user gesture.** The `AudioContext` is created/resumed lazily in `ensureAudio()`, called from Start (and other user actions) — required for sound to work on iOS/Safari. Never create or resume audio on load.
- **Timing must be wall-clock based.** Countdown is computed from `Date.now()` against `state.endTime`, not by decrementing a counter each interval. `setInterval` (250ms) only triggers re-render/expiry checks. This keeps the timer accurate when the screen briefly sleeps. A `visibilitychange` handler forces a re-sync on wake. Preserve this model for any timing change.

## Architecture

Everything is one IIFE in the `<script>` block. Key pieces:

- **`state` object** — the single source of truth: `modeKey`, `isBreak`, `running`, `endTime` (ms), `remaining` (seconds, authoritative while paused), `round`, `completed`, `tasks[]`, `activeTask`, `muted`. While running, time-left derives from `endTime`; while paused, from `remaining`.
- **`MODES`** — the three presets (`25/5`, `20/5`, `15/3`) keyed by focus minutes. `LONG_BREAK_AFTER = 4`.
- **Cycle logic** — `periodComplete()` drives transitions: focus end → increment `completed`, show long-break note every 4th focus, go to break; break end → `advanceTaskForward()` (cycles `activeTask`), bump `round` (wraps at 4), go to next focus. `switchTo(isBreak, autostart)` sets up a period and keeps running through transitions.
- **Persistence** — `load`/`save` wrap `localStorage` (JSON, try/catch). Keys are namespaced under `STORE` (`bdf_*`): title, message, tasks, active task, mode, completed count, muted. `completed` persists until "Reset Full Session".
- **Rendering** — `renderTimer()` and `renderTasks()` rebuild the relevant DOM from `state`; call them after any state mutation. `renderTasks()` recreates the task list and wires per-item handlers (set active via dot, edit via input, delete via ×).

## Conventions

- Colors and radii are CSS custom properties on `:root` (warm ivory/beige bg, translucent cream card, charcoal text, dusty-rose accent). Break mode is themed via the `.is-break` class on `#timerCard`. Adjust the design through these variables rather than hardcoding values.
- Keep buttons large and labels explicit for accessibility; layout is responsive for phone portrait, iPad, and landscape monitor.
- After editing state, always persist (if relevant) and re-render — there is no reactive framework.
