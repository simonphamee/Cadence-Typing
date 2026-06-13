# Cadence

A real-time typing-speed trainer that runs entirely in the browser. Pick a passage, start typing — watch your words-per-minute take shape as you go.

Built with vanilla JavaScript, the Web Audio API, and the Canvas 2D API. No frameworks, no build step, no dependencies. One HTML file.

> This project was built using AI-paired development with [Claude](https://claude.ai). I directed the architecture, features, and UX decisions; Claude wrote the implementation. More on that [below](#how-this-was-built).

## Demo

Open `index.html` in any modern browser. Pick a content mode and a test length, then just start typing — the clock starts on your first keystroke. Press `Tab` at any time to restart with a fresh passage.

## Features

**Three content modes**
- **Words** — continuous, natural multi-paragraph prose spanning science, history, fiction, and technology, with real capitalization and punctuation
- **Code** — authentic syntax fragments across JavaScript, Python, SQL, and Java, full of brackets, operators, and symbols
- **Quotes** — public-domain literature, philosophy, and proverbs

**Timed tests and difficulty**
- Three test lengths: 15 / 30 / 60 seconds
- **Sudden Death** — the first incorrect keystroke instantly stops the clock, flashes the screen red, and drops in a fresh passage
- **Blind** — correctly typed letters fade away as you go, forcing your eyes onto the upcoming text
- Endless text — passages auto-extend, so even a 300 WPM run never reaches the end mid-test

**Real-time analytics**
- Live countdown and live WPM update as you type
- End-of-run results screen with headline WPM, accuracy, raw WPM, and character counts
- A smooth per-second **WPM line chart** drawn on canvas, showing where your speed spiked, dipped, or held steady
- A **trouble-keys** list flagging every character you missed more than twice

**Interactive controls**
- Light / dark theme toggle — deep plum-charcoal with neon amber, or crisp off-white with slate-blue
- Synthesized mechanical keystroke audio with a mute toggle
- Per-character color feedback, a smooth sliding caret, an error shake, and line-by-line auto-scroll

## Architecture

Everything lives in one `index.html`, organized into four labeled sections:

```
Section 1 — UI Layout         HTML + CSS, themed via CSS custom properties
Section 2 — Text Engine       Passage generation, word/character rendering, caret + scroll
Section 3 — Input & Scoring   Hidden-field capture, case-sensitive matching, live stats
Section 4 — Render Loop        Timer, per-second WPM logging, canvas chart, results
```

**Input pipeline:**
```
keystroke → hidden <input> → input event → glyph match vs. target char → DOM feedback + stats
```

**Sound pipeline (no audio files):**
```
correct key → Web Audio oscillator → fast-decay envelope → speakers
```

**Key engineering decisions:**
- Words render as inline-block units that never split across rows, and spaces are real break points — so text wraps into clean lines and the viewport scrolls down naturally
- Vertical scroll is computed from the active character's live `offsetTop` against the measured line height, so it tracks correctly across themes and viewport sizes
- Input is read as the actual glyph the keyboard produced — capitals and shifted punctuation arrive already resolved — so scoring is case- and shift-correct without parsing raw keycodes, and IME composition is guarded against false keystrokes
- The passage auto-extends whenever the cursor nears the end, guaranteeing an endless stream even at 300 WPM, with no mid-test reset
- WPM is sampled once per whole second inside the timer loop; the results chart smooths those points with a Catmull-Rom spline on a `devicePixelRatio`-aware canvas
- Keystroke sounds are synthesized on the fly — short oscillator blips with a quick attack and fast decay, a crisp click for letters and a lower thud for the spacebar — so there are zero audio assets to load
- Colors live in CSS custom properties with a `[data-theme="light"]` override; the canvas chart reads the same tokens (including `--accent-rgb`) so it always matches the active theme

## How This Was Built

I built this project through iterative prompting with Claude (Anthropic's AI assistant). I didn't write the code by hand — I described what I wanted at each step, reviewed the output, tested it in the browser, and directed the next feature.

What I brought to the process: the product vision, feature prioritization, and UX decisions — the amber "ink" aesthetic, the rhythm-themed identity, the difficulty modifiers, and the instrument-panel analytics screen — plus the iterative build order that kept the app playable at every step.

What Claude brought: the implementation — the typing engine, the word-wrapping and auto-scroll math, the Web Audio keystroke synthesis, the Canvas performance chart, and the specific engineering solutions like the case-correct input handling and the endless-buffer rendering.

I'm sharing this honestly because I think the collaboration itself is interesting, and because misrepresenting AI-generated code as hand-written helps nobody.

## Tech

HTML · CSS · JavaScript · Web Audio API · Canvas 2D

## Browser Support

Any modern browser with Web Audio and Canvas support (Chrome, Firefox, Safari, Edge). Keystroke audio initializes on your first key, since browsers require a user gesture to start audio. The intended fonts (JetBrains Mono and Inter) load from Google Fonts; offline, the app falls back to your system's monospace font and remains fully playable.

## License

MIT
