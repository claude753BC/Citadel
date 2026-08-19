# Citadel — project brief

This document is the design record for the Citadel app: what was asked
for, what was built, and why, so this folder is self-contained without
needing the original chat.

## Original request

Build a personal productivity application including:
- A configurable Pomodoro timer.
- Inspirational quotes from philosophers and classical-world thinkers,
  with the Stoics emphasized specifically.
- Links to lofi/classical listening on YouTube, split into three
  flavors: Lofi, Lofi Classical, and Classical — always linking out to
  YouTube rather than embedding playback.
- A custom graphic in the app's own visual style, inspirational in
  tone.
- Freedom to propose additional features and to rename the app to
  something better than a generic working title.

Follow-up requests, in order:
1. Add a productivity log using **WOOP** (Wish, Outcome, Obstacle,
   Plan / mental contrasting with implementation intentions) and a
   plain to-do list — "change nothing else" about the existing app.
2. Add a live 24-hour clock at the top of the page, in the same visual
   style, so the time is visible at a glance.
3. Explore porting the single-file HTML app into a standalone desktop
   application (this folder is the result — a Tauri wrapper).

## Naming and design direction

- **Name: Citadel** — after the "inner citadel" from Marcus Aurelius /
  Pierre Hadot's reading of the *Meditations*: the fortress of the mind
  nothing external can breach. Tagline: "build your inner citadel."
  (Alternatives floated but not chosen: Meridian, Agora, Hegemonikon.)
- **Signature visual element: a sundial.** The Pomodoro timer isn't a
  generic progress ring — it's a hand-built SVG sundial with Roman
  numeral hour marks, 60 minute ticks, a gnomon, and a bronze shadow
  wedge that sweeps clockwise as the session elapses. This was a
  deliberate choice to avoid the generic "circular progress bar"
  default and tie the timer literally to the classical-antiquity theme
  (a sundial shadow "consuming" the day maps well onto Stoic ideas
  about the scarcity of time).
- **Palette:** dark quarried-stone background (`#17181a` /
  `#1f2123`), parchment/marble text (`#ece4cf` / `#f5f1e6`), oxidized
  bronze as the primary accent (`#b0873f` / `#d9ae5e`), a weathered
  bronze-green "patina" as a secondary accent (`#5c7d72`), and a
  restrained sealing-wax red (`#8b3a3a`) reserved for rare emphasis.
  Chosen specifically to avoid the common AI-generated defaults (warm
  cream + terracotta; near-black + one neon accent) while still fitting
  the antiquity subject matter.
- **Type:** Cormorant Garamond for display/headings, EB Garamond for
  body text, IBM Plex Mono for the countdown digits and clock (loaded
  from Google Fonts with system-serif fallbacks in the stack).
- **Hero graphic:** a bespoke SVG — three columns, a rising sun behind
  the center column, a laurel motif — paired with Marcus Aurelius's
  line about rising at dawn "to do the work of a human being."

## Content decisions

- **Quotes:** ~40 curated entries, tagged `stoic` or `classical`.
  Selection is weighted so Stoic voices (Marcus Aurelius, Seneca,
  Epictetus, plus Zeno, Cleanthes, Musonius Rufus) surface roughly 70%
  of the time even under the "All Voices" filter; a "Stoics Only" chip
  narrows it further. Other classical figures included: Aristotle,
  Plato, Socrates, Cicero, Heraclitus, Pythagoras, Epicurus. A new
  quote surfaces automatically whenever a focus session completes.
- **Soundscape links:** built from four YouTube URLs supplied in chat.
  One (the "1990s Lofi City" rain playlist) was confirmed by title via
  search; the other three could not be confirmed, so they're labeled
  neutrally ("Featured Stream I/II", "Classical Radio Mix") rather than
  guessing at content. Each category tab also links out to a live
  YouTube search for that mood, since "today's top playlist" changes
  daily and can't be hard-coded reliably.
- **WOOP log:** four fields (Wish, Outcome, Obstacle, then a
  "then I will…" action), with a live preview line that assembles the
  actual if-then implementation intention as you type. Entries persist
  in a scrollable log, newest first, deletable individually.
- **To-do ledger:** minimal — add, check off, delete, clear completed.
  No due dates or priority levels; kept deliberately simple to match
  the rest of the app's restraint.
- **24-hour clock:** small, top of page, mono digits in bronze, a tiny
  radiating-sun glyph beside it. Deliberately unobtrusive — doesn't
  compete with the hero art or the sundial timer.

## Architecture notes

- Originally a single self-contained HTML file (`citadel.html`) built
  as a Claude.ai artifact, using the `window.storage` persistence API
  available in that sandboxed environment for settings, stats, the
  WOOP log, and to-dos.
- **This folder is the desktop port.** The app logic, markup, and CSS
  are unchanged from the web version. The only functional change: the
  storage layer now uses `localStorage` (see `storeGet`/`storeSet` near
  the top of the `<script>` block in `dist/index.html`) instead of the
  Claude-artifact-only API, since `localStorage` is safe and correct in
  a real desktop webview.
- Chose **Tauri** over Electron for the native wrapper: the existing
  HTML/CSS/JS ports over essentially untouched, and Tauri produces a
  much smaller, lighter app (~10-20MB vs Electron's ~150MB+) by using
  the OS's built-in webview instead of bundling Chromium — worth it for
  something meant to sit open all day.

## File map

- `dist/index.html` — the entire app (markup, styles, logic).
- `src-tauri/` — the Rust/Tauri native shell: `Cargo.toml`,
  `tauri.conf.json` (window size, bundle identity, icon paths),
  `src/main.rs`, `build.rs`.
- `icon-source.png` / `icon-source.svg` — 1024×1024 source art (the
  sundial motif) that `tauri icon` expands into the full icon set.
- `README.md` — the literal build commands, in order.
- `cowork-prompt.txt` — a ready-to-paste task instruction for Cowork to
  drive the build end to end.
- `PROJECT-BRIEF.md` — this file.
