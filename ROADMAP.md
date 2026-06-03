# Logseq Brain — Roadmap

## Shipped

### v1.0.0 — Self-hosted fork + v0.7.0 fixes
- Repo restructured as self-hosted marketplace (`logseq-brain`): plugin moved to `plugins/logseq-brain/`, `marketplace.json` added at repo root. Install via `/plugin marketplace add vlad-aleksandrov/LogseqBrain` + `/plugin install logseq-brain@logseq-brain`.
- **Durable config location**: config moved from plugin cache root (wiped on reinstall/upgrade) to `~/.config/logseq-brain/config.json` (XDG standard). Legacy `.brain-config.json` at plugin root still read as fallback with migration warning.
- **`gitAutoPush` config key**: brain-save now commits the graph after every save and optionally pushes when `"gitAutoPush": true` in config.
- **`HH:mm` time prefix on activity bullets**: journey-log entries now prefix each bullet with the current time from `date +%H:%M` — restores the quick-scan chronological signal that was removed in v0.6.0.
- **Logseq normalization tolerance**: journey-log and brain-save now Read before anchoring surgical Edits; `old_string` is derived from the actual file content (Logseq may normalize `- ## Heading` → `## Heading` and spaces → tabs).
- Authorship updated: Jan Mesarc → Vlad Aleksandrov. CONTRIBUTING.md stripped to personal developer notes.

### v0.6.0 — Journey log + token frugality
- Journey log (`## Activity` section in today's journal, one bullet per brain skill use)
- Progressive disclosure: each `SKILL.md` split into orchestrator + per-skill `references/` + cross-skill `skills/_shared/` (path-resolution, journey-log, staleness, section-locator)
- Section-targeted reads in brain-load (brief mode) and brain-save (grep-anchored) — meaningful token reduction on large project pages
- Description tuning on all four skills (purpose-first frontmatter with explicit "Don't fire for X" scope boundaries)
- ROADMAP rewrite (Shipped/Current/Future), CLAUDE.md `_shared/` convention, CONTRIBUTING.md v0.6.0 manual validation checklist
- New `.brain-config.json` `journeyLog` toggle (default `true`)

### v0.5.0 — Intelligence layer
- Stale-context detection on load
- Decision conflict detection on save (mark old as `superseded`)
- Session continuity hints (last-worked-on, open questions)
- Auto-suggest save (suggestion only — never persists without confirmation)

### v0.4.0 — Multi-project & integration
- Cross-project decision log (`pages/Decisions.md`)
- Multi-project session support
- Project status dashboard (`brain-status` skill)
- Jira task pointer pattern (task ID + folder + summary, no plan duplication)

### v0.3.0 — Richer context
- Cross-project search ("what do we know about X")
- Smart context budgeting (brief vs. full load modes)
- Richer session log (files-modified, skills-used, related-tickets, open-questions properties)
- Meta page auto-population from session-discovered preferences

### v0.2.0 — Hardened MVP
- Fuzzy project name matching
- Project-page existence guard in brain-save
- `journals/` directory creation in brain-init
- Logseq format compliance (bullets, properties, namespaced filenames)

### v0.1.0 — MVP
- Three skills: `brain-init`, `brain-load`, `brain-save`
- Save/load cycle against a Logseq graph
- Initial graph layout (`pages/`, `journals/`, `Index.md`, `Meta.md`)

## Current — v1.1.0 (backlog)

- **Activity-line examples in all four SKILL.md** — update to show `HH:mm` prefix format (minor doc consistency pass).
- **`brain-load` Logseq normalization hardening** — same Read-before-Edit hardening applied in v1.0.0 to brain-save/journey-log; apply to brain-load's section reads.
- Other candidates from the Future list promoted once Logseq's roadmap clarifies which is closest to ready.

## Future — informed by Logseq's own roadmap

These items are deferred until Logseq's own work makes them clearly worthwhile.

- **Storage abstraction layer.** Decouple "what to store" from "how to store it" so the backend can swap from markdown files to Logseq's DB API without changing the skill surface. Defer until Logseq's "Markdown Mirror" feature ships and the DB version stabilizes — until then, markdown is safe.
- **Logseq DB plugin API integration.** Replace markdown file IO with Logseq's plugin API once it goes GA. Per Logseq's roadmap the API is in development; revisit when stable.
- **Headless sync via new Logseq CLI.** The new Logseq CLI supports headless sync without the desktop app, which would let LogseqBrain operate concurrently with the desktop app safely. Revisit when the CLI ships in stable.
- **Conflict resolution for Logseq Sync.** Detect and merge sync conflicts gracefully (relevant once we're touching the same files Logseq Sync is touching mid-write).
- **Graph analytics.** "brain stats" — counts of decisions, sessions, projects, activity over time.
- **Plugin packaging refinements.** Cleaner Cowork install flow, installation wizard.
