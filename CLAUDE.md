# CLAUDE.md — AI Assistant Guide for `patterns`

## Project Overview

This repository is a **single-file, zero-build React application** — a Design Patterns Navigator and Code Smell Diagnostic Tool for software engineers. It is served as a static HTML file with no package manager, bundler, or backend.

- **Entry point / entire codebase:** `index.html` (≈800 lines, ≈150 KB)
- **License:** GNU GPL v3
- **Author:** Piyush Prateek

---

## Architecture: Single-File React App

All application code lives inside `index.html`. There are no separate JS, CSS, or component files.

### Structure of `index.html`

| Lines (approx.) | Content |
|---|---|
| 1–22 | HTML shell, `<meta>` tags, CDN `<script>` tags |
| 23–107 | `patternsData` — array of 67 design pattern objects |
| 108–131 | `smellsData` — array of 20 code-smell symptom objects |
| 132–152 | UI constants (`patCatColors`, `compColor`, `smellCatColors`, `sevColors`) |
| 153–168 | `decisionTree` — nested object for interactive decision flow |
| 169–212 | Decision tree node definitions (6 problem domains) |
| 213–250 | `SLabel()` and `Pill()` helper components |
| 251–340 | `NavigatorTab()` — browse/search/filter patterns |
| 341–395 | `DiagnosticTab()` — map code smells to patterns |
| 396–416 | `App()` — root component, tab switching, `ReactDOM.render()` |

### Dependencies (CDN only — no npm)

```html
<!-- React 18.2.0 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<!-- Babel standalone 7.23.9 (for JSX in-browser transpilation) -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.9/babel.min.js"></script>
<!-- Google Fonts -->
```

There is **no `package.json`**, **no `node_modules`**, **no build step**.

---

## Running the App

```bash
# Option 1 — open directly in a browser
open index.html

# Option 2 — serve locally (avoids some browser restrictions)
python3 -m http.server 8080
# then open http://localhost:8080
```

No installation, compilation, or environment setup is needed.

---

## No Build, Test, or Lint Commands

This project intentionally has **no** tooling:

| Tool | Status |
|---|---|
| npm / yarn / pnpm | Not used |
| Webpack / Vite / Rollup | Not used |
| ESLint / Prettier | Not configured |
| Jest / Vitest | Not configured |
| TypeScript | Not used |
| CI/CD | Not configured |

Do **not** add a build system or test framework unless explicitly asked. The single-file design is intentional for portability.

---

## Data Model Conventions

### Pattern Object (`patternsData` array)

```js
{
  name: "Factory Method",          // string — unique pattern name
  category: "Creational",          // string — one of 7 categories (see below)
  complexity: 1,                   // number — 1 (Low), 2 (Med), 3 (High)
  problem: "...",                  // string — the pain point this solves
  when: "...",                     // string — when to apply it
  avoid: "...",                    // string — when NOT to apply it
  example: "...",                  // string — concrete real-world scenario
  howItWorks: "...",               // string — brief mechanism description
  pairs: ["Builder", "..."],       // string[] — complementary patterns
  tags: ["creation", "..."],       // string[] — searchable keywords
  python: "...",                   // string — Python code example
  realWorld: "..."                 // string — non-code real-world analogy
}
```

**Pattern categories** (7 total, with icon + hex color defined in `patCatColors`):

| Category | Icon |
|---|---|
| Creational | 🏗️ |
| Structural | 🧱 |
| Behavioral | 🎭 |
| Data & Persistence | 💾 |
| Infrastructure | ⚙️ |
| Caching | ⚡ |
| Architectural | 🏛️ |

### Code Smell Object (`smellsData` array)

```js
{
  id: "constructor-hell",          // string — kebab-case unique identifier
  symptom: "Constructor Hell",     // string — human-readable label
  description: "...",              // string — what it means
  codeSmell: "// code example",   // string — illustrative bad code snippet
  category: "creation",           // string — one of 6 smell categories
  severity: "high",               // string — "critical" | "high" | "medium"
  patterns: [
    { name: "Builder", fit: "perfect" },   // fit: "perfect" | "good" | "sometimes"
    { name: "Factory Method", fit: "good" }
  ]
}
```

**Smell categories** (6): `creation`, `structure`, `behavior`, `data`, `resilience`, `architecture`

**Severity levels** (3): `critical`, `high`, `medium`

---

## UI/Styling Conventions

- **Dark theme only:** Background `#090912`, primary text near-white
- **Inline styles exclusively:** No CSS classes, no external stylesheets, no CSS-in-JS libraries
- **Responsive:** Centered container, `max-width: 880px`, flexbox layout
- **Semantic color mapping:** Each category has a hex color + icon defined in constants (`patCatColors`, `smellCatColors`, etc.)
- **Complexity badge colors:** defined in `compColor` object
- **Severity badge colors:** defined in `sevColors` object

All styling changes must use the inline `style={{}}` prop pattern.

---

## React Conventions

- **Functional components only** — no class components
- **Standard React hooks:** `useState`, `useMemo`, `useCallback`
- **No JSX file extension** — Babel standalone handles transpilation at runtime
- **Component naming:** PascalCase (`NavigatorTab`, `DiagnosticTab`, `App`, `Pill`, `SLabel`)
- **All components are defined at the top level of the `<script type="text/babel">` block** — no module imports/exports
- **`ReactDOM.render()`** is called at the bottom of the script block (React 17 API; upgrade to `createRoot` only if React version is bumped)

---

## Key Components

### `SLabel(props)`
Renders a styled section header label. Props: `children` (text).

### `Pill(props)`
Renders a badge/chip. Props: `label`, `color`, `onClick` (optional — if provided, becomes clickable).

### `NavigatorTab()`
- Search input filters across `name`, `problem`, `when`, `example`, `tags`, `python`, `realWorld`
- Category filter pills
- Complexity sort/filter
- Decision tree interactive flow (6 domains → sub-decisions → pattern recommendations)
- Pattern card expanded on click to show full details including Python code examples and real-world analogies

### `DiagnosticTab()`
- 20 code smell symptom cards
- Expanding a card shows matched patterns with their fit level
- Severity and category badges

### `App()`
- Two tabs: Navigator | Diagnostic
- Header with title and description
- Tab state managed with `useState`

---

## Modifying Pattern or Smell Data

When adding, editing, or removing patterns/smells:

1. **Patterns** — edit the `patternsData` array (lines ≈29–107).
2. **Smells** — edit the `smellsData` array (lines ≈108–131).
3. **Decision tree** — edit the `decisionTree` object (lines ≈153–212).
4. **Category colors/icons** — edit `patCatColors` or `smellCatColors` (lines ≈132–152).
5. Keep `pairs` and `patterns` cross-references consistent (pattern names must match exactly).
6. Tags should be lowercase and descriptive.

---

## Git Workflow

```bash
# Current default branch for AI development
git checkout claude/add-claude-documentation-1xYTo

# Commit convention (plain English, no emoji unless asked)
git commit -m "Add X pattern to patternsData"
git commit -m "Fix Y symptom fit assessment"

# Push
git push -u origin claude/add-claude-documentation-1xYTo
```

- The `master` branch is the main branch.
- Feature work for AI sessions goes on `claude/...` branches.
- Remote: `http://local_proxy@127.0.0.1:48938/git/piyushprateek/patterns`

---

## What NOT to Do

- Do **not** introduce a package manager or build system.
- Do **not** split the file into multiple files unless explicitly requested.
- Do **not** add TypeScript, ESLint, Prettier, or test frameworks without being asked.
- Do **not** use CSS classes or external stylesheets — use inline `style={{}}`.
- Do **not** use class-based React components.
- Do **not** import external libraries other than what is already on CDN.
- Do **not** change the GPL v3 license.
- Do **not** create a README.md unless asked (this CLAUDE.md is the documentation file).

---

## Quick Reference: Pattern Count by Category

| Category | Count |
|---|---|
| Creational | 7 |
| Structural | 8 |
| Behavioral | 14 |
| Data & Persistence | 8 |
| Infrastructure | 17 |
| Caching | 4 |
| Architectural | 9 |
| **Total** | **67** |

Code smells: **20** | Decision tree domains: **6**
