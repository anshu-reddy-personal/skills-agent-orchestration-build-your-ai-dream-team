# Project Pulse Implementation Plan

## Summary

**Project Pulse** is a lightweight static dashboard for Mona’s contributors. It helps the team quickly see which projects are active, who owns each one, current status, recent activity, and priority/risk—presented as a polished card-based UI rather than a bare HTML page.

**Outcome of this work:** a runnable static app under `app/` plus a VS Code launch configuration so learners can preview the dashboard with **Run Project Pulse Dashboard**. Deliverables:

| File | Purpose |
| --- | --- |
| `app/index.html` | Dashboard page titled **Project Pulse**, loads styles and data, renders visible project cards |
| `app/styles.css` | Polished, accessible, responsive styling (`.dashboard`, `.project-card`, badges, priority treatment) |
| `app/project-data.json` | Top-level `projects` array with `name`, `owner`, `status`, `recentActivity`, `priority` |
| `.vscode/launch.json` | Strict JSON launch config named **Run Project Pulse Dashboard** serving `app/` and opening `index.html` |

**Agent roles (orchestration only—no agent git commits):**

- **Orchestrator** — phases work, assigns exclusive file scopes, runs parallel/sequential tasks, integrates and reports.
- **Planner** — this plan (no code).
- **Coder** — structure, data, client logic, launch config.
- **Designer** — UI/UX, accessibility, visual design, CSS polish.
- **Learner** — all git stage/commit/push via Copilot CLI prompts.

---

## Ordered implementation steps

### Step 1 — Establish project data (Coder)

Create sample Project Pulse data that drives the UI.

**Requirements:**

- Valid JSON file at `app/project-data.json`.
- Top-level key: `"projects"` (array).
- At least **3–5** realistic sample projects so the dashboard looks populated.
- Each project object **must** include:
  - `name` (string)
  - `owner` (string)
  - `status` (string; e.g. Active, On Hold, At Risk, Completed)
  - `recentActivity` (string; short human-readable update)
  - `priority` (string; e.g. High, Medium, Low)
- Optional extra fields (e.g. `summary`) are allowed if they do not break the required shape; keep the schema simple unless Designer requests a contributor-friendly summary field that Coder can add in the same file ownership window.
- Prefer deterministic, contributor-friendly values (no empty strings, no missing keys).

**Owner:** Coder only  
**Files:** `app/project-data.json`

---

### Step 2 — Build dashboard structure and launch config (Coder) **in parallel with Step 3**

Implement the HTML shell, data binding, and VS Code launch support.

#### 2a. `app/index.html` (Coder)

- Exact visible title: **Project Pulse** (page title and primary heading).
- Reference `styles.css` (stylesheet link).
- Reference/load `project-data.json` (e.g. `fetch` or equivalent client-side load).
- Root layout hook: container with class `dashboard`.
- Render **visible** project cards from the `projects` array.
- Each card uses class name **`project-card`**.
- Each card shows at least: name, owner, **status**, **recentActivity**, and **priority** (these strings should appear in markup/content so validation can find them).
- Status presented as a badge-friendly element (e.g. class hooks like `status-badge` or `status-*`) so Designer can style without rewriting structure.
- Priority presented with a clear hook (e.g. `priority`, `priority-high`) for visual treatment.
- Semantic, accessible markup: landmark/header, headings hierarchy, lists or articles for cards, adequate text alternatives if any icons are used.
- Graceful empty/error messaging container if data fails to load (visible, simple text).
- No build step; plain static HTML + minimal inline or same-file script is fine. Keep JS small and deterministic.
- Do **not** own visual polish in CSS beyond what is required in HTML class hooks; leave styling to Designer in `app/styles.css`.

#### 2b. `.vscode/launch.json` (Coder)

- Strict JSON: **no comments**.
- Must parse with `python3 -m json.tool`.
- One configuration named exactly: **`Run Project Pulse Dashboard`**.
- Serve from the app directory: `cwd` = `${workspaceFolder}/app`.
- Start command: `python3 -m http.server 5500` (or equivalent `shell`/`node-terminal` launch that runs that command with the correct cwd).
- `serverReadyAction` opens **`http://localhost:%s/index.html`** so the browser shows the dashboard, **not** a directory listing.
- Prefer a deterministic config (`type`, `request`, `name`, command, cwd, port/URI format) that works in Codespaces/VS Code Run and Debug.

**Owner:** Coder only  
**Files:** `app/index.html`, `.vscode/launch.json`

---

### Step 3 — Design polished dashboard styling (Designer) **in parallel with Step 2**

Create a finished frontend look—not a bare browser-default page.

**Requirements for `app/styles.css`:**

- Include selector **`.dashboard`** (layout wrapper: max-width, spacing, grid/flex for cards).
- Include selector **`.project-card`** (card surface).
- Polished treatment: **`border-radius`**, **`box-shadow`**, readable typography, consistent spacing, clear visual hierarchy.
- Status badges: distinct, high-contrast pills/chips tied to Coder’s HTML hooks.
- Priority treatment: color, weight, or label styling so High/Medium/Low (or risk) is scannable at a glance.
- Responsive layout: usable on narrow and wide viewports (card grid wrapping, comfortable touch targets, no horizontal overflow).
- Accessibility: sufficient color contrast, focus styles if interactive, respect heading/spacing clarity; avoid color-only meaning where practical (pair color with text).
- Optional: subtle page background, header styling for “Project Pulse”, muted meta text for owner/activity.
- Do **not** modify `app/index.html`, `app/project-data.json`, or `.vscode/launch.json` in this step—style only via agreed class hooks from this plan / Coder’s HTML.

**Owner:** Designer only  
**Files:** `app/styles.css`

---

### Step 4 — Integrate and fix file-scoped issues (Coder + Designer, sequential, non-overlapping scopes)

After Steps 2 and 3 complete, Orchestrator reviews the integrated UI.

- **Coder** only if structure/data/launch bugs: `app/index.html`, `app/project-data.json`, `.vscode/launch.json`.
- **Designer** only if visual/a11y/CSS gaps: `app/styles.css`.
- If HTML needs an extra class hook for styling/a11y, Coder adds the hook in `app/index.html` first; Designer then updates `app/styles.css` in a follow-up micro-step (still no shared simultaneous file writes).
- Confirm cards render from JSON, styles apply, launch opens `index.html`.

**Owners:** Coder and Designer with **exclusive** file scopes per fix pass  
**Files:** same four deliverables as needed

---

### Step 5 — Local validation pass (Orchestrator coordinates; Coder/Designer fix only on failure)

Run through validation expectations below. Orchestrator reports gaps; specialists fix only within assigned files. Learner runs **Run Project Pulse Dashboard** from Run and Debug and confirms the browser shows Project Pulse cards.

**No new feature files** in this step. Git remains learner-controlled.

---

## File assignments by step

| Step | Agent | Files (create/modify) | Must not touch |
| --- | --- | --- | --- |
| 1 | **Coder** | `app/project-data.json` | `app/index.html`, `app/styles.css`, `.vscode/launch.json` |
| 2 | **Coder** | `app/index.html`, `.vscode/launch.json` | `app/styles.css`, `app/project-data.json` (unless a tiny schema fix is required and scoped) |
| 3 | **Designer** | `app/styles.css` | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` |
| 4–5 | **Coder** and/or **Designer** | Only files in their row above, as assigned by Orchestrator | Anything outside explicit scope |

**Required files covered:** `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`.

**Note:** `docs/project-pulse-plan.md` is produced from this plan by the learner/Orchestrator save step; agents do not implement app code while planning. Later exercise handoff (`docs/final-handoff.md`) is **out of scope** for build steps 1–5 but is the next orchestration milestone after validation.

---

## Designer and Coder responsibilities

### Coder owns

- Runnable static app **structure and behavior**.
- `app/project-data.json` schema and sample content (`projects` + required fields).
- `app/index.html`: title **Project Pulse**, links to `styles.css`, loads `project-data.json`, renders `.project-card` elements showing status, recentActivity, and priority.
- Minimal deterministic client script to map JSON → DOM.
- Explicit error/empty states in the UI structure.
- `.vscode/launch.json`: strict JSON, name **Run Project Pulse Dashboard**, `cwd` `${workspaceFolder}/app`, `python3 -m http.server 5500`, `serverReadyAction` → `http://localhost:%s/index.html`.
- Validation of JSON parseability and basic load behavior.
- Does **not** own polished visual design in `app/styles.css` unless Orchestrator explicitly assigns a CSS bugfix after Designer is done.

### Designer owns

- UI/UX direction for a **polished frontend dashboard**.
- `app/styles.css` only (in the main build phases): `.dashboard`, `.project-card`, status badges, priority treatment, spacing, typography, `border-radius`, `box-shadow`, responsive layout, contrast/focus.
- Information hierarchy: title → project grid → card fields scannable in seconds.
- Accessibility guidance realized through CSS (and requested HTML hooks, implemented by Coder).
- Does **not** implement launch config, JSON data files, or primary HTML/JS structure unless Orchestrator assigns a narrowly scoped HTML pass (prefer Coder for HTML).

### Orchestrator owns (coordination only)

- Phase gating, parallel vs sequential decisions, exclusive file scopes, integration review, reporting.
- Does **not** write implementation code.
- Does **not** git stage/commit/push.

---

## Dependencies between steps

```text
Step 1 (project-data.json)
    ↓
    ├──────────────────────────────┐
    ↓                              ↓
Step 2 (index.html + launch.json)  Step 3 (styles.css)
    └──────────────┬───────────────┘
                   ↓
            Step 4 (integrate)
                   ↓
            Step 5 (validation)
```

| Dependency | Detail |
| --- | --- |
| Step 2 depends on Step 1 | HTML/JS should bind to the real `projects` field names from `project-data.json`. |
| Step 3 soft-depends on Step 1 | Designer can start CSS from agreed hooks without final copy, but badge/priority values benefit from knowing status/priority vocab in the data. |
| Step 3 does **not** hard-depend on Step 2 file completion | Designer styles agreed class names (`.dashboard`, `.project-card`, badge/priority hooks) defined in this plan; Coder must use those hooks in HTML. |
| Step 2 and Step 3 are independent on **file ownership** | No shared write files → safe to parallelize after Step 1. |
| Step 4 depends on Steps 2 and 3 | Integration only after both structure and styles exist. |
| Step 5 depends on Step 4 | Validate the integrated dashboard and launch path. |
| Launch config depends on `app/index.html` existing | Config can be authored in parallel with HTML in Step 2, but manual Run and Debug proof waits until HTML exists. |

**Agreed HTML/CSS contract (prevents rework):**

- Wrapper: `.dashboard`
- Card: `.project-card`
- Show text for status, recent activity, and priority in each card
- Prefer additional hooks: e.g. `.status-badge`, `.priority` (Coder adds in HTML; Designer styles in CSS)

---

## Parallel work decisions

### Can run in parallel

| Parallel unit | Agents | Files | Condition |
| --- | --- | --- | --- |
| **Step 2 ∥ Step 3** | Coder ∥ Designer | Coder: `app/index.html`, `.vscode/launch.json` · Designer: `app/styles.css` | Step 1 complete; both follow the class-hook contract above |

Within Step 2, Coder may create `app/index.html` and `.vscode/launch.json` together (same agent, no conflict).

### Must run sequentially

| Order | Why |
| --- | --- |
| Step 1 → Step 2 | Data shape should exist before (or tightly constrain) HTML binding. |
| Step 1 → Step 3 (recommended) | Status/priority vocabulary informs badge design. |
| (Step 2 and Step 3) → Step 4 | Need both structure and styles before integration. |
| Step 4 → Step 5 | Validate after integration fixes. |
| Any Coder HTML hook change → Designer CSS update | Avoid simultaneous edits to related concerns; still **different files**, but ordered. |
| Never parallelize two agents on the **same file** | Orchestrator must keep file scopes disjoint. |

### Explicit non-overlap rule

In any single phase, each file has **one** writer:

- `app/project-data.json` → Coder  
- `app/index.html` → Coder  
- `app/styles.css` → Designer  
- `.vscode/launch.json` → Coder  

---

## Edge cases to handle

1. **Empty `projects` array** — Show a clear empty state inside `.dashboard` (“No projects to show”) rather than a blank page.
2. **Missing/failed `project-data.json` load** — Show an explicit error message; do not fail silently.
3. **Malformed JSON** — Same as failed load; Coder keeps sample data valid; validate with `python3 -m json.tool`.
4. **Missing fields on a project** — Prefer complete sample data; defensive rendering (skip or show “Unknown”) if a field is absent so one bad object does not blank the page.
5. **Long text** — Long `name`, `recentActivity`, or owner strings should wrap inside cards without breaking layout.
6. **Many projects** — Grid should wrap responsively without overflow.
7. **Status/priority variety** — Include mixed statuses and priorities in sample data so badges/priority styles are demonstrable.
8. **Launch opens directory listing** — Must open `index.html` via `serverReadyAction` URL pattern `http://localhost:%s/index.html` and `cwd` under `app/`.
9. **Port already in use (5500)** — Document as a known local risk; learner stops prior server; config stays on **5500** for exercise determinism.
10. **`.vscode/launch.json` comments** — Forbidden; strict JSON only.
11. **CSS without hooks** — If Designer cannot style a control, request Coder add a class hook rather than Designer editing HTML in the same phase.
12. **Accessibility** — Contrast for badges on colored chips; do not rely on color alone for priority if avoidable; preserve readable font sizes.
13. **Git** — Agents must not stage/commit/push; learner handles git after build review.
14. **No framework/build toolchain** — Stay static (HTML/CSS/JSON + tiny JS); match the exercise’s small-app pattern.

---

## Validation expectations

### File existence

- [ ] `app/index.html`
- [ ] `app/styles.css`
- [ ] `app/project-data.json`
- [ ] `.vscode/launch.json`

### `app/project-data.json`

- [ ] Parses as JSON (`python3 -m json.tool app/project-data.json`)
- [ ] Top-level `"projects"` key
- [ ] Each project includes `name`, `owner`, `status`, `recentActivity`, `priority`

### `app/index.html`

- [ ] Contains exact title text **Project Pulse**
- [ ] References `styles.css`
- [ ] References `project-data.json`
- [ ] Renders visible elements with class `project-card`
- [ ] UI surfaces **status**, **recentActivity**, and **priority** (keyphrases present in file/rendered structure)
- [ ] Uses `.dashboard` wrapper class in markup

### `app/styles.css`

- [ ] Includes `.dashboard` selector
- [ ] Includes `.project-card` selector
- [ ] Includes `border-radius` and `box-shadow`
- [ ] Responsive card layout and badge/priority styling present
- [ ] Reads as a polished dashboard, not unstyled HTML

### `.vscode/launch.json`

- [ ] Parses as JSON (`python3 -m json.tool .vscode/launch.json`)
- [ ] No comments
- [ ] Configuration name: **Run Project Pulse Dashboard**
- [ ] Serves from app directory (`cwd` `${workspaceFolder}/app`)
- [ ] Uses `python3 -m http.server 5500`
- [ ] `serverReadyAction` opens `http://localhost:%s/index.html`
- [ ] Keyphrase `index.html` present

### Manual / learner preview

- [ ] Run and Debug → **Run Project Pulse Dashboard** → browser shows Project Pulse with project cards (not a directory listing)
- [ ] Stop the preview server after checking

### Process validation

- [ ] Coder and Designer stayed in assigned files per phase
- [ ] No agent git commit/push
- [ ] Orchestrator can describe who did what for the later handoff step

---

## Open questions

1. **Optional `summary` field** — The brief mentions a short contributor-friendly summary. Required grading fields are only `name`, `owner`, `status`, `recentActivity`, and `priority`. **Recommendation:** Coder may add an optional `summary` string per project and display it on the card if it stays simple; not required for exercise checks.
2. **Status/priority enumerations** — Exact allowed values are not mandated. **Recommendation:** use a small fixed set (e.g. status: Active / On Hold / At Risk / Completed; priority: High / Medium / Low) and mirror those in CSS modifiers where useful.
3. **Script placement** — Inline `<script>` in `index.html` vs tiny extra `.js` file. **Recommendation:** keep script in `index.html` to stay within the three known app files and avoid extra deliverables unless Orchestrator expands scope.
4. **Designer HTML pass** — Only if integration shows structural a11y gaps. **Recommendation:** default remains Designer = CSS only; Coder applies any required markup hooks.
5. **Sample project count and theme** — Not specified. **Recommendation:** 4 Mona/team-flavored projects with varied status and priority so the UI demonstrates badges and priority treatment.

---

## Orchestrator phase cheat sheet

| Phase | Mode | Agent(s) | File scope |
| --- | --- | --- | --- |
| A | Sequential | Coder | `app/project-data.json` |
| B | **Parallel** | Coder + Designer | Coder: `app/index.html`, `.vscode/launch.json` · Designer: `app/styles.css` |
| C | Sequential fix passes | Coder then Designer as needed | Exclusive scopes only |
| D | Validation | Orchestrator (+ specialists on failure) | Read-all; write only on assigned fixes |

**Success criteria:** Contributors opening **Run Project Pulse Dashboard** see a polished **Project Pulse** frontend with data-driven **project cards**, clear status badges and priority treatment, and valid static assets under `app/` plus `.vscode/launch.json`.
