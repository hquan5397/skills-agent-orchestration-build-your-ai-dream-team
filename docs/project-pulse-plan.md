# Project Pulse Implementation Plan

## Summary

Create a lightweight static **Project Pulse** dashboard that lets contributors scan multiple projects, including owner, status, recent activity, priority/risk, and a short summary. The repository currently has no app implementation or package/build system, so the solution should use plain HTML, CSS, JSON, and browser JavaScript, with VS Code launching `python3 -m http.server 5500` from `app/`.

## Ordered implementation steps

1. **Confirm the data contract and visual direction**
   - **Designer:** Define the dashboard information hierarchy, responsive card layout, status/priority visual treatment, color contrast, keyboard-focus treatment, and empty/error/loading states.
   - **Output contract for Coder:** `.dashboard` is the page wrapper and every rendered card uses `.project-card`; card content must visibly identify status, recent activity, and priority.
   - **Files:** Design decisions are implemented in `app/styles.css`; no separate design artifact is required.
   - **Depends on:** Project brief only.

2. **Create deterministic project data and runnable-preview configuration**
   - **Coder:** Create a valid JSON data source with a top-level `projects` array and multiple representative projects.
   - Every project must contain `name`, `owner`, `status`, `recentActivity`, and `priority`; also include a concise contributor-facing `summary` to fulfill the brief.
   - **Coder:** Create a strict, comment-free VS Code launch configuration that serves `app/` and opens the dashboard file rather than a directory listing.
   - **Files:**
     - `app/project-data.json` — Coder owns.
     - `.vscode/launch.json` — Coder owns.
   - **Launch requirements:** Configuration name exactly `Run Project Pulse Dashboard`; working directory exactly `${workspaceFolder}/app`; command uses `python3 -m http.server 5500`; `serverReadyAction` opens `http://localhost:%s/index.html`.
   - **Depends on:** Step 1's data/display expectations for the JSON shape; launch work has no dependency on application markup.

3. **Implement the polished stylesheet**
   - **Designer:** Create the responsive visual implementation based on step 1.
   - Include `.dashboard` and `.project-card` hooks, clear spacing and typography, rounded cards (`border-radius`), elevation (`box-shadow`), readable badges, priority treatment that does not depend solely on color, and a narrow-screen layout.
   - Provide visible keyboard focus styles and sufficient foreground/background contrast.
   - **File:** `app/styles.css` — Designer owns exclusively.
   - **Depends on:** Step 1. It may proceed alongside step 2.

4. **Implement markup and dynamic card rendering**
   - **Coder:** Create a semantic document titled exactly `Project Pulse`, link `styles.css`, and include an identifiable dashboard heading and project-list region.
   - Fetch/reference `project-data.json`, validate that `projects` is an array, and render each project as a visible `.project-card`.
   - Display the project name, owner, status, `recentActivity`, priority, and summary. Use DOM element/text APIs rather than inserting raw JSON as HTML.
   - Include loading, empty-list, and data-load failure messaging in an announced status region so the UI remains understandable if data cannot render.
   - **File:** `app/index.html` — Coder owns exclusively.
   - **Depends on:** Steps 2 and 3: the JSON schema must be stable and CSS hooks must be agreed before final markup is produced.

5. **Integrate and validate**
   - **Coder:** Run structural checks and launch the dashboard through VS Code.
   - **Designer:** Review the rendered dashboard at desktop and narrow widths for hierarchy, readability, focus visibility, and responsive card behavior.
   - **Files:** No new application files; changes are limited to the owning agent's file if remediation is needed.
   - **Depends on:** Steps 2–4 being complete.

## File assignments

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic dashboard shell, stylesheet/data references, fetch/render behavior, project cards, and user-facing loading/empty/error states. |
| `app/styles.css` | Designer | Polished responsive dashboard styling, `.dashboard` and `.project-card` hooks, badges, priority treatment, accessibility and focus styling. |
| `app/project-data.json` | Coder | Valid fixture data with top-level `projects`; required project fields plus contributor-friendly summary. |
| `.vscode/launch.json` | Coder | Strict JSON launch support for `Run Project Pulse Dashboard`, serving from `app/` and opening `index.html`. |

## Dependencies and execution order

- **Sequential**
  1. Designer establishes hierarchy/hooks and Coder confirms the data contract.
  2. Coder finalizes `app/index.html` only after the JSON schema and CSS hooks are stable.
  3. Integration validation follows all implementation work.

- **Parallel**
  - Coder can create `app/project-data.json` and `.vscode/launch.json` in parallel because they do not overlap.
  - Designer can implement `app/styles.css` in parallel with Coder's data and launch work.
  - File ownership is non-overlapping during parallel work, preventing conflicts.

## Edge cases and risks

- Fetching JSON from a `file://` URL may fail; validation must use the local HTTP launch configuration.
- A malformed JSON file, missing `projects` array, missing fields, or an empty array must not leave a blank/broken page; show an explicit state.
- JSON values should be rendered as text, not injected as HTML.
- Status and priority values may expand or be unfamiliar; layout should tolerate long text and not rely only on color to communicate meaning.
- Port `5500` can already be occupied; stop an existing preview or resolve the local conflict before validating.
- The launch configuration must use strict JSON with no comments and must open `/index.html`, not the server root.

## Validation expectations

1. **File and syntax checks**
   - All four assigned files exist.
   - `python3 -m json.tool app/project-data.json` succeeds.
   - `python3 -m json.tool .vscode/launch.json` succeeds.
   - Data has top-level `projects`; each project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.

2. **Required content checks**
   - `app/index.html` contains `Project Pulse`, references `styles.css` and `project-data.json`, and renders `.project-card` elements with visible status, recent activity, and priority.
   - `app/styles.css` includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
   - `.vscode/launch.json` contains `Run Project Pulse Dashboard` and an `index.html` browser target.

3. **Manual runtime and UX checks**
   - Run **Run Project Pulse Dashboard** from VS Code Run and Debug.
   - Confirm the server starts from `app/`, the browser opens `http://localhost:5500/index.html` (via the configured `%s` URL), and no directory listing is shown.
   - Confirm multiple cards render from JSON, content is readable at desktop and narrow viewport widths, badges/priorities are understandable, and keyboard focus is visible.
   - Temporarily simulate empty or unavailable data during local review if practical, confirming the user-facing fallback state is clear; restore valid fixture data afterward.

## Open questions

- The brief requires a short contributor-friendly summary but does not prescribe its JSON key; this plan uses `summary`. Confirm this name if a different schema is preferred.
- No canonical status or priority vocabulary is provided. Use a small consistent set (for example, `On track`, `At risk`, `Blocked`; `High`, `Medium`, `Low`) unless Mona's team has established terminology.
