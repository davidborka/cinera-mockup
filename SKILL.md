---
name: cinera-clickable-mockup
description: Build a fully clickable, multi-theme, responsive HTML mockup of the Cinera AI-driven music-video editor (single-file React-free vanilla JS). Use whenever the user wants to prototype, demo, or pitch the Cinera-style timeline editor with working drag-drop, transport, theming, and mobile layout.
type: skill
version: 1.0
---

# Cinera Clickable Mockup — SKILL

> **Mission:** turn the Cinera reference screenshot into a *living*, *clickable*, *responsive* prototype that survives a real demo (drag-drop scenes, scrub the playhead, switch themes, flip to mobile) — without a build step. One `index.html`, three themes, two devices.

This skill is modeled on `alchaincyf/huashu-design` (HTML-as-tool, not as medium) but specialised for **DAW-style timeline editors**: dense info, transport controls, drag-drop reordering, color-coded structure, and dual desktop/mobile flows.

---

## 0. Design Critique of the Reference

Before re-implementing, name the problems honestly. Otherwise the clone inherits them.

| # | Problem in the original | Why it hurts | Fix in the clone |
|---|---|---|---|
| 1 | **Three timeline-like rows** (waveform + scene-order strip + Idővonal nézet) | Redundant scrolling surfaces; user loses sense of "the truth" | Promote ONE timeline as canonical; demote the bottom strip to a **mini-map** with viewport rectangle |
| 2 | **Section colors are candy-shop** (cyan, blue, orange, purple, green, magenta with no system) | Colors should encode meaning, not vibe | Use a **semantic 6-step ramp** (intro → outro) on a single hue-wheel arc; saturation = energy |
| 3 | **Purple-on-navy waveform** clashes with section pills | Two unrelated palettes overlap on the same surface | Waveform inherits the *active section's* color at low opacity |
| 4 | **Right-panel scrollbar invisible**, "Haladó beállítások" looks like CTA | Users mistake settings for the primary action | Right panel becomes an inspector with section headers; CTA reserved for `Render` |
| 5 | **Floating "Tweaks" panel** competes with bottom transport | Two persistent overlays on the same edge = decision paralysis | Tweaks docks into the right inspector as a tab; FAB only on mobile |
| 6 | **Avatars on character lanes** are cute but uninformative | They don't show *which scenes* the character is in | Lane spans become draggable range-pills with handles |
| 7 | **No mobile story** | Tool ships dead-on-arrival for half the audience | Mobile = 4-tab bottom nav (Timeline / Scenes / Inspector / Render); transport bar above the keyboard |
| 8 | **Buttons inconsistent** (some pill, some square, some icon-only with no tooltip) | Visual chaos and accessibility miss | Single `--radius-control` token; every icon-only button gets `aria-label` and tooltip |
| 9 | **Typography is one-note** (single weight, single family, similar sizes) | Hierarchy collapses on dense screens | Display/UI pairing (Inter Tight + JetBrains Mono for timecodes) with a 1.2 scale |
| 10 | **Dark navy + neon purple** = generic "AI SaaS slop" | Looks like every other 2024–25 launch page | Three named themes, each with a **single non-purple accent** to break the bias |

---

## 1. The Three Themes (no purple gradients)

Each theme is a complete token set: surfaces, ink, accent, waveform, section ramp, shadow recipe.
Switch via `data-theme` on `<html>`; persist in `localStorage`.

### 1.1 `midnight` — Studio at 2am
- **Surface:** `oklch(18% 0.015 240)` graphite, **not** navy
- **Ink:** `oklch(96% 0.01 240)`
- **Accent:** `oklch(72% 0.18 35)` — molten amber. Reads as "record light", not as "AI".
- **Section ramp:** monochrome graphite → amber, energy via lightness
- **Waveform:** amber on graphite, 60% opacity outside the active section
- **Mood:** late-night focus, cinema booth

### 1.2 `daylight` — Studio Atelier
- **Surface:** `oklch(98% 0.005 90)` paper, with `oklch(94% 0.01 90)` panels
- **Ink:** `oklch(20% 0.02 240)` ink-blue, never pure black
- **Accent:** `oklch(58% 0.16 155)` — fern green, pairs with paper without screaming
- **Section ramp:** desaturated rainbow (the only place color is allowed to "sing")
- **Waveform:** ink-blue, 80% opacity
- **Mood:** Monday morning, daylight on the desk

### 1.3 `aurora` — Cinematic Neon
- **Surface:** `oklch(12% 0.03 200)` deep teal-black
- **Ink:** `oklch(94% 0.02 200)`
- **Accent:** `oklch(78% 0.22 320)` magenta + secondary `oklch(80% 0.18 200)` cyan (duotone)
- **Section ramp:** magenta → cyan along structure
- **Waveform:** vertical magenta→cyan gradient
- **Mood:** music-video poster, festival projection

> Rule: **only one theme uses a saturated rainbow ramp.** The others stay disciplined. Variety lives between themes, not inside one.

---

## 2. Layout Anatomy (desktop, ≥1280px)

```
┌────────────────────────────────────────────────────────────────────┐
│  TOPBAR   logo · project crumbs                 user · render      │  56px
├──────┬──────────────────────────────────┬──────────────────────────┤
│      │  PROJECT HEAD  cover · meta · transport-mini       │       │
│ NAV  ├──────────────────────────────────┤                  │       │
│ 220  │  TIMELINE TABS (waveform/beats/structure)            │       │
│      │  SECTION STRIP   (drag to retime)                    │ INSP │
│      │  WAVEFORM         (click to seek, drag selection)    │ 320  │
│      │  SCENE-CARDS-LANE (click=focus, drag=reorder)        │       │
│      │  CHARACTER LANES  (drag handles to retime)           │       │
│      ├──────────────────────────────────┤                   │       │
│      │  SCENE ORDER STRIP (HTML5 DnD reorder)               │       │
│      ├──────────────────────────────────┤                   │       │
│      │  MINIMAP (viewport rect, pinch to zoom)              │       │
├──────┴──────────────────────────────────┴───────────────────────────┤
│  TRANSPORT  prev · back · play · fwd · loop · time · vol · fullscr │  64px
└────────────────────────────────────────────────────────────────────┘
```

The mini-map replaces the redundant third timeline row from the original.

---

## 3. Mobile Layout (<768px)

Stop pretending mobile is "the same screen smaller". Restructure.

```
┌─────────────────────────┐
│  TOPBAR   logo · render │  44px
├─────────────────────────┤
│                         │
│  TAB CONTENT            │
│  (Timeline | Scenes |   │
│   Inspector | Render)   │
│                         │
├─────────────────────────┤
│  TRANSPORT MINI         │  56px
├─────────────────────────┤
│  ⌒ Tabs: 🎬 🎞 ⚙ 🚀     │  64px (safe-area)
└─────────────────────────┘
```

- Timeline tab: vertical scroll of section pills; pinch-zoom waveform.
- Scenes tab: vertical card list, long-press to drag.
- Inspector tab: same content as desktop right panel.
- Render tab: credit balance, queue, `Render` CTA.

---

## 4. Mandatory Interactions (the "fully working" bar)

If any of these doesn't work, the mockup is a screenshot, not a prototype.

| Interaction | Implementation note |
|---|---|
| **Theme switch** | `data-theme` on `<html>`; `localStorage.cinera.theme`; transition `background-color 200ms` only (not all properties — avoid jank) |
| **Device switch** | iframe-style preview frame for mobile; same DOM, query string `?device=mobile` |
| **Play / pause** | `requestAnimationFrame` loop advances `--t` CSS var → playhead `transform: translateX(calc(var(--t) * 1%))` |
| **Spacebar** | Toggles play. Don't preventDefault inside text inputs. |
| **Click waveform** | Set playhead, update timecode, dispatch `seek` custom event |
| **Drag selection on waveform** | Pointer events; show range with `<rect>` overlay |
| **Drag-drop scene cards** | HTML5 native: `draggable=true`, dragover for placeholder slot, drop reorders array, re-renders |
| **Drag character lane handles** | Pointer events on left/right handle; clamp to lane bounds; live preview |
| **Tabs** (Hullámforma / Ütemek / Szerkezet) | `[role=tablist]` with arrow-key nav |
| **Snap dropdown** | Real `<select>` styled, not fake div |
| **Volume / playhead sliders** | Real `<input type=range>` with custom thumb |
| **Notifications popover** | Click outside to close; ESC to close |
| **Render dropdown split-button** | Primary action + caret submenu |
| **Auto-fit toggle** | Real checkbox; persists |
| **Inspector "Tweaks"** | Tab inside inspector on desktop; FAB on mobile |
| **Sidebar nav** | Hash routing, active state |
| **Keyboard shortcuts** | `?` opens cheat-sheet modal |

Every icon-only control gets `aria-label` and a `title` tooltip. No exceptions.

---

## 5. Implementation Rules

1. **One file**, vanilla JS, no build step. Open `index.html` directly.
2. **No external network** dependencies at runtime — bundle Inter via `@font-face` from a CDN that's already cached, but degrade to system stack if offline.
3. **CSS layers**: `@layer reset, tokens, layout, components, themes, motion, utilities` — themes override tokens, never components.
4. **Tokens via `oklch`**, not hex. Hex is allowed only at the very edge (font color of one-off badges).
5. **No purple gradient anywhere.** Specifically banned: `linear-gradient(..., #8b5cf6, ...)`. The reference's #1 AI-slop tell.
6. **No emoji as iconography.** Use inline SVG with `currentColor`.
7. **State is plain objects** in module-scope; render functions are idempotent; no framework.
8. **Drag-drop must work with keyboard too** — arrow keys reorder when a card is focused (`Cmd/Ctrl + ↑/↓`).
9. **`prefers-reduced-motion`** disables the playhead animation but keeps the seek interaction.
10. **All text from the reference stays Hungarian.** Don't quietly translate — that's not your call.

---

## 6. Verification Checklist (run before shipping)

- [ ] Open `index.html` directly via `file://` — no console errors, no missing assets.
- [ ] Tab through entire UI with keyboard only — every control reachable, focus visible.
- [ ] Switch all three themes — no element disappears (contrast survives).
- [ ] Drag a scene from position 7 to position 2 — order persists in local state.
- [ ] Play for 30s — playhead reaches the right place, audio-free is fine but timecode advances.
- [ ] Resize to 375×812 (iPhone) — bottom tabs appear, no horizontal scroll.
- [ ] `prefers-reduced-motion: reduce` — playhead jumps don't animate.
- [ ] Lighthouse a11y ≥ 95.

---

## 7. Anti-Slop Hard Rules (Cinera-specific)

- **Never** show a stock "AI brain" or "neural net" decorative graphic.
- **Never** put "powered by AI" anywhere visible — the user knows.
- **Never** animate the logo on idle. It's a logo, not a screensaver.
- **Never** use the same purple as Spotify, Discord, Figma, Linear, or Vercel. (You will be tempted. Don't.)
- **Never** stack three timelines. Pick one.
- **Never** put a CTA inside a settings panel.

---

## 8. Files Produced

- `index.html` — the mockup (single-file, ~1400 LoC, vanilla JS)
- `SKILL.md` — this document

Open in a browser. That's the whole pipeline.
