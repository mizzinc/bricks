# bricks-acss-rules

A Claude AI project knowledge base for building websites with **Bricks Builder** and **Automatic CSS (ACSS 3.x)**.

These files define the conventions, token system, and code generation rules used to convert visual designs into Bricks-ready HTML, CSS, and JSON — consistently, across any project.

---

## What this is

This repo is the single source of truth for a Claude-assisted Bricks/ACSS workflow. Instead of re-explaining conventions every session, these files are loaded into a Claude Project so every conversation starts with full context.

The primary workflow is **design to code** — provide a screenshot or design reference, get back paste-ready Bricks JSON or HTML/CSS. The rules in these files ensure the output is always structured correctly, uses the right tokens, and follows consistent naming conventions.

---

## Files

| File | Purpose |
|---|---|
| `RULES.md` | Master ruleset — element mapping, BEM naming, JSON schema, CSS patterns, output format, session start protocol |
| `ACSS-TOKENS.md` | Complete ACSS token catalogue — every available variable by category. Values supplied per-project via `:root` paste |
| `ACSS-AUTO.md` | What ACSS handles automatically — section padding, buttons, cards, typography defaults. Never duplicate these in custom CSS |
| `ACSS-CHEATSHEET.md` | Full utility class reference with breakpoint modifier syntax |
| `OUTPUT-MODES.md` | Two output modes — Bricks-ready (default) and Standalone preview with ACSS simulation |

---

## How to use with Claude

### Option A — Upload to Claude Project Knowledge (recommended for speed)

1. Create a Claude Project at [claude.ai](https://claude.ai)
2. Go to **Project Knowledge → Add content**
3. Upload all five `.md` files
4. Add the Project Instructions below

Files are loaded automatically at the start of every conversation in the project.

**When rules change:** re-upload the updated file to Project Knowledge to replace the old version.

### Option B — Fetch from GitHub at session start

Add the raw URLs to Project Instructions and Claude will fetch the latest version at the start of each conversation:

```
At the start of every conversation, fetch and read:
https://raw.githubusercontent.com/[username]/bricks-acss-rules/main/RULES.md
https://raw.githubusercontent.com/[username]/bricks-acss-rules/main/ACSS-AUTO.md
https://raw.githubusercontent.com/[username]/bricks-acss-rules/main/ACSS-TOKENS.md
https://raw.githubusercontent.com/[username]/bricks-acss-rules/main/ACSS-CHEATSHEET.md
https://raw.githubusercontent.com/[username]/bricks-acss-rules/main/OUTPUT-MODES.md
```

**Tradeoff:** always up to date, but adds a fetch step at the start of each conversation.

### Hybrid (best of both)

GitHub is the source of truth and version history. Re-upload to Project Knowledge after significant updates. Fast at runtime, clean to maintain.

---

## Project Instructions (paste into Claude Project)

```
You are assisting a senior WordPress developer build websites in Bricks Builder using ACSS 3.x.

Before responding to any build request, read:
- RULES.md — conventions, naming, JSON schema, CSS patterns
- ACSS-AUTO.md — what ACSS handles automatically (do not duplicate)
- ACSS-TOKENS.md — available token catalogue
- ACSS-CHEATSHEET.md — utility class reference
- OUTPUT-MODES.md — output format rules

Primary workflow: convert visual designs (screenshots, Figma exports, references) 
directly to Bricks-ready HTML/CSS or JSON. Apply all conventions automatically.

Never stall on ambiguity. Always produce complete, paste-ready output.
Use the CSS resolution hierarchy: utility class → ACSS variable → raw value + FLAG.
Default output mode: Bricks-ready.
```

---

## Session start template

Paste this at the start of every build conversation:

```
Client: [Client Name]

:root {
  /* paste full DevTools :root output here */
}

Typeface: [heading font / body font]
Active button colour: [btn--neutral / btn--primary / btn--base]

First component: [screenshot or description]
```

The `:root` paste is the single source of truth for token values on that project. Until provided, Claude falls back to `ACSS-TOKENS.md` defaults.

---

## Multi-project setup

One Claude Project for all clients. Client context is provided at session start via the two-line header above — lightweight enough to be habit, specific enough to keep builds separate.

For clients with significantly different ACSS configurations or custom component libraries, create a separate Claude Project and note the key differences in that project's instructions.

---

## Key conventions (summary)

**Naming**
- All custom classes use `bt-` prefix with BEM: `bt-[block]__[element]--[modifier]`
- Card components: `bt-[name]-card` (triggers ACSS card framework)
- Distinct components get their own block name — `bt-poster` not `bt-hero-poster`
- Modifiers describe the variation, never the page: `bt-hero--centered` not `bt-hero--about`

**CSS**
- All component CSS lives in the root block's global class
- Use `%root%` for every selector in a global class CSS block — never hardcode the class name
- Double selector for framework overrides: `%root%-card%root%-card { border: 0; }`
- `overflow: hidden` breaks `position: sticky` — always `overflow: unset` before applying sticky
- Build sticky offsets from tokens: `calc(var(--header-height) + var(--section-padding-block) + ...)`

**ACSS**
- Use `bg--dark` not `bt-hero--dark` — ACSS handles the full colour relationship cascade
- Fine-tune within a bg context by reassigning the token: `--bg-dark-heading: var(--white)`
- Card framework: always add new card classes to Manual Card Selectors in ACSS → Cards → Targeting Settings
- `list--none` not custom CSS for list resets

**JSON**
- Root section has `"parent": 0` (integer, not string)
- All CSS in the root block's global class — child global class objects have empty settings
- Always set `tag` explicitly on `heading` and `text-basic` elements

---

## Contributing / updating

When a new pattern is discovered from a real build:
1. Update the relevant `.md` file
2. Commit with a clear message: `Add %root% double specificity pattern`
3. Re-upload to Claude Project Knowledge if using Option A

The rules improve through real production output — not documentation guessing.

---

*Built by Michael van Dinther / Mizzinc*  
*Stack: Bricks Builder + Automatic CSS 3.x + Claude*
