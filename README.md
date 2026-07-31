# CortexOS

A concept demo built around one central idea: the AI assistant isn't a bolted-on app sitting alongside everything else — it's positioned as the operating system's **control layer**, with the standing ability to rearrange the entire desktop, window layout, and system theme from a single typed or spoken instruction, instead of you dragging and clicking everything into place yourself.

It ships as two fully interactive, self-contained desktop simulations — a **Windows-style environment** and a **macOS-style environment** — both built around the same underlying idea: a persistent AI control layer that can reorganize the whole workspace on command.

> **This is a front-end demo, not a real operating system or a real AI.** Every "app" runs entirely in your browser tab, all data is stored in `localStorage`, and the AI control layer is a scripted sequence of DOM changes — there is no LLM call, no network request, and no real speech recognition anywhere in this project. See [How "AI" actually works here](#how-ai-actually-works-here) for the honest breakdown.

## Quick start

There is no build step, no server, and no dependencies to install.

1. Open `index.html` in a modern desktop browser (Chrome, Edge, or Firefox recommended).
2. Pick **Windows Environment** or **macOS Environment**.
3. Press **Ctrl+Space** (or click the sparkle icon) to open the AI control layer.

That's it — everything is client-side. You can also open `windows.html` or `macos.html` directly to skip the launcher.

## What's actually in the box

```
CortexOS/
├── index.html     # launcher — choose Windows or macOS
├── windows.html   # Windows-style desktop simulation
├── macos.html     # macOS-style desktop simulation
└── README.md
```

Each of the three pages is a single self-contained HTML file (Tailwind CSS via CDN + vanilla JavaScript, no framework, no bundler). `windows.html` and `macos.html` are independent — they don't share a script file — but they're built from the same architecture, so if you understand one, you understand the other. See the comments at the top of each `<script>` block for an orientation.

## The AI control layer

Both environments have an identical AI layer, just re-skinned for the OS it lives in:

| | Windows | macOS |
|---|---|---|
| Open it | `Ctrl+Space`, or the sparkle icon on the taskbar | `Ctrl+Space`, or the sparkle icon in the menu bar (where Siri would live) |
| Talk to it | Type a sentence, or click the mic for simulated voice input | Same |

You can type things like:
- `"enter focus mode"` / `"switch to creative mode"` / `"let's relax"` — triggers one of the three preset workspace transformations below
- `"open notepad"` / `"close the browser"` — opens or closes a specific app by name
- Anything else gets a graceful "here's what I can actually do" fallback instead of silently failing

### The three workspace modes

Each mode is a scripted, narrated sequence — the AI layer logs each step it's "taking" (e.g. *"Closing entertainment apps…"*, *"Snapping windows side-by-side…"*) with a short pause between steps so the reorganization is visible rather than instant.

- **Focus** — closes distraction apps (browser, media player), opens the Code Editor and Terminal snapped side-by-side, dims non-essential desktop elements, and shifts the whole environment to a dark, distraction-free blue theme.
- **Creative** — closes unrelated apps, opens Design Studio in a wide layout with a small reference-file panel, and shifts to a warm, high-contrast theme.
- **Relax** — closes productivity apps, opens and centers the Music/Media player, hides taskbar/Dock running-app badges, and shifts to a soft warm evening theme.
- **Reset** — reverts the theme, dimming, and badge changes without closing anything.

### Simulated voice input

Click the microphone icon to enter a "listening" state. Instead of using a real microphone, it shows a list of example phrases grouped by intent (rearrange the workspace / open an app / close an app). Clicking one simulates a transcription typing itself into the input field, then runs it through the exact same command parser as typed text.

### Explainer bubbles

Hovering the AI trigger, the mic button, each mode preset, or any desktop icon shows a small tooltip explaining what that feature does — and, for apps specifically, whether it's a fully working simulation or a static placeholder (see the table below).

## What's actually functional vs. a placeholder

This project mixes real, working mini-apps with static mockups. Nothing is hidden — every desktop icon has a hover bubble stating which category it falls into, and here's the same information in one place:

| App | Windows name | macOS name | Status |
|---|---|---|---|
| File browser | This PC | Finder | **Placeholder** — static drive/folder tiles, not a real file browser |
| Trash | Recycle Bin | Trash | **Placeholder** — always shows empty, no real delete/restore |
| Text editor | Notepad | TextEdit | **Functional** — type, then File → Save/Open persists text to `localStorage` |
| Browser | Microsoft Edge | Safari | **Partly functional** — address bar, mock search, and back/forward history work; results are fabricated, there's no real internet access |
| Settings | Settings | System Settings | **Functional** — page navigation and the Dark Mode toggle really work; device/network/Bluetooth info is static |
| Terminal | Command Prompt | Terminal | **Functional** mini shell — supports `help`, `dir`, `date`, `time`, `cls`, `echo` |
| Code Editor | Code Editor | Code Editor | **Functional** text editor with live line numbers (not persisted between sessions) |
| Design Studio | Design Studio | Design Studio | **Functional** — real freehand drawing on an HTML canvas with brush/eraser/color tools |
| Media player | Media Player | Music | **Simulated** — play/pause and a progress bar run on a timer; there's no real audio file |

## How "AI" actually works here

To be explicit about what's real and what isn't:

- **No model, no API calls.** `detectMode()` and `findAppInText()` are plain regex/substring matching against a small keyword list — not natural language understanding.
- **No speech recognition.** The mic button never requests microphone access. It reveals a list of pre-written example phrases; picking one plays a typing animation and then runs the same text-command parser used for keyboard input.
- **The "reorganization" is a fixed script.** Each mode function (`runFocusMode`, `runCreativeMode`, `runRelaxMode`) is a hand-written sequence of window-manager calls with `await sleep(ms)` between them, purely so a human can watch each step happen.

The goal of this project is to make the *interaction pattern* of an AI-driven OS — the AI positioned as the control layer, not a bolted-on app — tangible and demoable, not to actually implement one.

## Window manager features (both environments)

- Drag windows by their title bar; text on the desktop and inside windows never gets accidentally selected mid-drag.
- Windows can't be dragged fully off-screen — at least part of the title bar always stays reachable.
- Resize from the bottom-right corner.
- Minimize, maximize/restore, and close, each with a short eased animation.
- Drag a window to the left/right screen edge to snap it to half-width; drag it away to restore its original size. Drag to the top edge to maximize.
- Click a taskbar/Dock icon to toggle its window: opens it if not running, restores+focuses it if minimized, minimizes it if already focused, or focuses it if running but unfocused.
- Right-click the desktop for a themed context menu (Refresh/Clean Up flashes the desktop, "New Text Document/File" adds a real, savable file to the desktop).
- A working light/dark theme toggle (Settings → Personalization on Windows, Settings → Appearance on macOS) that also feeds the AI control layer's per-mode accent colors.

## Tech stack

- Plain HTML/CSS/JS — no build tooling
- [Tailwind CSS](https://tailwindcss.com/) via the Play CDN, for utility layout only
- Inline SVG for every icon (no icon fonts, no image assets, no external requests besides the Tailwind CDN script)
- `localStorage` for the only "persistence" that exists (theme, saved text files, desktop icons)

## Known limitations

- Single window instance per app — opening an already-running app focuses/restores it rather than opening a second copy.
- No real file system, no real network access, no real audio or speech.
- Not tested for mobile/touch input — this is a desktop-pointer (mouse) experience.
- The two environments are independent files with duplicated logic by design (each is meant to be readable end-to-end on its own), so a fix in one does not automatically apply to the other.

## License

No license has been added yet — treat this as "all rights reserved" until a `LICENSE` file is added.
