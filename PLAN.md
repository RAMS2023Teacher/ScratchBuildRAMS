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

### Phase 4 — Student accounts + Google Classroom
See §3 for the full account design and §4 for the anti-distraction controls
that accounts make possible.

## 3. Student accounts

**Use "Sign in with Google" — no new usernames or passwords.** Students already
have district Google accounts, so there's nothing for you or IT to manage, and
nothing new to forget.

| Decision | Recommendation | Why |
|---|---|---|
| Login | Google sign-in, **district domain only** | Outside/personal Gmail accounts are rejected. No passwords stored by us. |
| Who's a teacher? | A short teacher email list kept by you (later: auto-detect from Google Classroom) | Teachers get the dashboard and approval tools; everyone else is a student. |
| Rosters / sections | Imported from **Google Classroom** (your 6th/7th/8th sections) | Ties each student to their CS class and period — needed for §4 scheduling. |
| Where projects are saved | The **student's own Google Drive** (a "RAMS Code" folder) | Student work stays inside the district's Google Workspace, which IT already trusts. We never hold copies of student projects. |
| Turning in | "Turn in to Classroom" button attaches the project to the assignment | No more download → upload step. |
| What our server stores | Only: email, name, section, approved Arcade games, Arcade settings, and a simple play log | Minimal data = much easier privacy approval. |

**Backend:** Firebase (Google) for sign-in, a small database, and security rules.
It's free at school scale and pairs naturally with Google Workspace.
*Check with IT:* Firebase is under Google Cloud terms, not the Workspace for
Education agreement, so it may need its own entry on the district's approved-app /
data-privacy (MA Student Privacy Alliance DPA) list. Fallback: the district can
host the backend itself.

**What students can do without signing in:** use the editor and save to their
computer (Phase 1 behavior). Signing in only unlocks Drive saving, turning in,
and the Arcade.

## 4. Keeping it from distracting other teachers' classes

The editor is just a coding tool, so the risk is the **Arcade** (student games).
The approach: **the Arcade is closed by default** and only opens when a CS
teacher says so. Layered controls, cheapest first:

1. **Separate web addresses for the editor and the Arcade.**
   e.g. `…/editor` and `…/arcade`. IT's existing web filter (GoGuardian,
   Securly, etc.) can then block or schedule `/arcade` on its own, and teachers who
   use classroom-management tools can block it in their own room — without
   breaking the editor. Zero extra work for us, and IT keeps the final say.

2. **Arcade hours tied to CS class periods (needs accounts).**
   Each student can only open the Arcade **during their own CS period** (from the
   RAMS bell schedule + Classroom roster) and optionally before/after school.
   Outside those times they see: *"Arcade is closed — opens during your CS class."*
   This is enforced by the **server's security rules using the server clock**, not
   just by hiding a button, so students can't get around it by changing their
   laptop clock or editing the page.

3. **Teacher "Open Arcade" switch.**
   Even during CS class, the Arcade stays closed until you press *Open Arcade for
   Period 3* on your dashboard (with an auto-close timer, e.g. 15 minutes). This
   is the on/off control you originally wanted — it makes sense now that
   accounts exist.

4. **Any staff member can pause a student or the whole Arcade.**
   A simple staff page: any teacher signed in with a district account can hit
   *Pause Arcade for [student]* or *Pause for everyone* if they see it in their
   room. You get an email/notice so you know.

5. **Play log for accountability.**
   Your dashboard shows who opened which game and when. Knowing it's logged is
   a deterrent by itself, and it settles "was he playing in math?" questions.

6. **Make Arcade games feel like coding, not gaming.**
   - Games open **in the editor with the code visible** (no full-screen-only
     player mode outside class).
   - Optional per-play time limit (e.g. 5 minutes, then "Remix it or move on").
   - Approval checklist for the gallery: finished, school-appropriate, and not an
     endless "idle clicker" style game.

**Recommended starting set:** #1 (free) + #2 + #3 + #5. Add #4 if other
teachers report problems; #6 is a classroom-culture choice for you.

## 5. Why IT should approve it (pitch summary)

- **No public community**: no chat, comments, or strangers; no unvetted games.
- **Teacher-curated content only** in the Arcade, which is **closed by default**
  and opens only during CS class when the teacher turns it on.
- **Doesn't follow students into other classes**: the Arcade has its own address
  IT can filter, any staff member can pause it, and play is logged.
- **District Google sign-in only**; no passwords stored, outside accounts rejected.
- **Minimal student data**: name, school email, section. Projects stay in the
  student's own district Google Drive.
- **AI runs locally** in the browser (Teachable Machine) — no data leaves the device.
- **Open source & auditable**: all code is in this GitHub repo.
- **Free** to host, and IT can keep scratch.mit.edu blocked.
## 6. Things to check first

- **Ask IT what's blocked exactly.** If they block `scratch.mit.edu` but the editor
  loads assets from `cdn.assets.scratch.mit.edu` (sprite/sound library), we'll
  either bundle those assets ourselves or ask IT to allow just that asset domain.
- **Existing alternatives** to compare against (worth listing in the IT request):
  Scratch's official offline desktop app, and TurboWarp (another open-source
  Scratch editor). Ours adds the school gallery + AI blocks + RAMS branding.

## 7. Legal / licensing notes

- The Scratch editor code is **AGPL-3.0**. We can modify and host it, but we must
  keep the source public (this repo does that) and keep the license file. Add a
  "Source code" link in the editor's About menu.
- **"Scratch", the Scratch logo, and the Scratch Cat are trademarks** of the
  Scratch Foundation. Our version must use its own name and default sprite
  (e.g. a RAMS mascot). Name ideas: *RAMS Code*, *RamBlocks*, *Ram Lab*.
- Keep a credit line: "Based on Scratch, developed by the Lifelong Kindergarten
  Group at the MIT Media Lab."

## 8. Next steps

1. Pick a name and base (plain Scratch editor vs. MIT PRG RAISE Playground).
2. Build Phase 1 and deploy it to GitHub Pages for a test link.
3. Bring the test link + §5 to IT for approval (include the account and
   anti-distraction plan from §3–§4 so they see the whole picture up front).
4. Ask IT: is Firebase OK, or should the backend be district-hosted?
5. Add accounts, the Arcade with its controls, and AI blocks.
