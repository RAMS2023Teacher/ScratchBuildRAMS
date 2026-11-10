# ScratchBuildRAMS — Project Plan

A school-controlled block-coding editor for Robert Adams Middle School, built from
the open-source Scratch editor. Students get the full editor (plus AI blocks) but
**no access to the public Scratch community** — the only projects they can browse
are ones made by RAMS students and approved by a teacher.

---

## 1. The key insight: the editor and the games are separate

Scratch is really two pieces of software:

| Piece | What it is | Open source? | Do we use it? |
|---|---|---|---|
| **The editor** (`scratch-gui`, `scratch-vm`, `scratch-blocks`) | The block-coding workspace, stage, sprites, sounds | Yes (AGPL-3.0) | **Yes** |
| **The community site** (scratch.mit.edu: Explore, Trending, Studios, comments) | Where the millions of games live | Not the part we need | **No** |

Running our own copy of the editor gives students **zero** access to the public
game library by default — there's nothing to "filter out". The only way in is to
reach scratch.mit.edu, which stays blocked.

## 2. What we build

### Phase 1 — RAMS editor (static site, no accounts, no data stored)
- Fork the Scratch editor, rebrand it (see §5 for naming rules), and host it on
  **GitHub Pages** (or a district-owned domain if IT prefers).
- Remove/hide anything pointing to scratch.mit.edu (Share button, "See Project
  Page", community links, tutorials that link out).
- Students save with **File → Save to your computer** and turn the `.sb3` in on
  Google Classroom like any other file. That alone works with Classroom today.

### Phase 2 — RAMS Gallery (school-only games)
- A "RAMS Arcade" page listing student projects **a teacher has approved**.
- Simplest version: approved `.sb3` files live in this repo (`gallery/`) with a
  `gallery.json` list. The teacher is the only one who can add to it → full control.
- Students click a project → it opens in the editor (play or "remix" it).
- No comments, no likes, no public profiles — just the games.

### Phase 3 — AI blocks
Bring in MIT's AI extensions so it's "AI-first":
- **MIT PRG RAISE Playground** (`mitmedialab/prg-raise-playground`) — MIT Media
  Lab's own fork of the Scratch editor with AI extensions (Teachable Machine,
  text classification, etc.). Option: use this as our base instead of plain Scratch.
- **Teachable Machine** blocks — students train an image/sound/pose model in the
  browser and use it in their game. **Runs on the student's device; nothing is
  sent to a server** (important for privacy approval).
- Later/optional: chatbot-style blocks that call an LLM. These send student text
  to a third party, so they need a separate privacy review — hold off until
  Phases 1–3 are approved.

### Phase 4 — Google Classroom integration (optional)
- "Sign in with Google" restricted to the district domain.
- "Save to Google Drive" / "Turn in to Classroom" buttons using the Google Drive
  and Classroom APIs (teacher creates the assignment; the editor attaches the `.sb3`).
- **Recommendation: skip the on/off switch.** Since the editor contains no outside
  games, there's nothing to switch off. The approved-only gallery *is* the control.
  An on/off switch adds a server, accounts, and approval complexity for little gain.

## 3. Why IT should approve it (pitch summary)

- **No student data collected** in Phases 1–3: no accounts, no logins, no server
  database. Projects stay on the student's computer / Google Drive.
- **No public community**: no chat, comments, or strangers; no unvetted games.
- **Teacher-curated content only** in the gallery.
- **AI runs locally** in the browser (Teachable Machine) — no data leaves the device.
- **Open source & auditable**: all code is in this GitHub repo.
- **Free** to host (GitHub Pages) and IT can keep scratch.mit.edu blocked.
- Phase 4 (Google sign-in) only uses the district's existing Google Workspace,
  which already has a signed data-privacy agreement.

## 4. Things to check first

- **Ask IT what's blocked exactly.** If they block `scratch.mit.edu` but the editor
  loads assets from `cdn.assets.scratch.mit.edu` (sprite/sound library), we'll
  either bundle those assets ourselves or ask IT to allow just that asset domain.
- **Existing alternatives** to compare against (worth listing in the IT request):
  Scratch's official offline desktop app, and TurboWarp (another open-source
  Scratch editor). Ours adds the school gallery + AI blocks + RAMS branding.

## 5. Legal / licensing notes

- The Scratch editor code is **AGPL-3.0**. We can modify and host it, but we must
  keep the source public (this repo does that) and keep the license file. Add a
  "Source code" link in the editor's About menu.
- **"Scratch", the Scratch logo, and the Scratch Cat are trademarks** of the
  Scratch Foundation. Our version must use its own name and default sprite
  (e.g. a RAMS mascot). Name ideas: *RAMS Code*, *RamBlocks*, *Ram Lab*.
- Keep a credit line: "Based on Scratch, developed by the Lifelong Kindergarten
  Group at the MIT Media Lab."

## 6. Next steps

1. Pick a name and base (plain Scratch editor vs. MIT PRG RAISE Playground).
2. Build Phase 1 and deploy it to GitHub Pages for a test link.
3. Bring the test link + §3 to IT for approval.
4. Add the gallery and AI blocks.
