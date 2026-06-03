# Journey Log

Every brain skill records a single-line entry under `## Activity` in today's journal whenever it runs. This is the "journey trail" — terse, time-ordered, distinct from the rich `## Sessions` summary that `brain-save` writes.

## When to call this

Each skill calls journey-log **once**, after completing its main work. Failures during the main work skip the journey-log call (don't log incomplete work).

## Input

A single `activity-line` string describing what happened. No leading bullet marker — this module adds the bullet.

Examples:

- `"loaded [[Projects/LogseqBrain]] (brief)"`
- `"saved [[Projects/LogseqBrain]]"`
- `"searched \"strategy pattern\" · 3 hits"`
- `"viewed dashboard"`
- `"initialized graph at /path/to/ClaudeBrain"`
- `"created project [[Projects/MyProject]]"`

Before writing, get the current time via Bash: `date +%H:%M`. Prepend it to the activity line: e.g., `"14:32 loaded [[Projects/LogseqBrain]] (brief)"`. This gives the activity trail a scannable chronological signal (Logseq does not auto-render `created-at::` block metadata visibly).

## Algorithm

1. **Read config.** Read `~/.config/logseq-brain/config.json` (falling back to `.brain-config.json` in the plugin root per `path-resolution.md`). If it contains `"journeyLog": false`, return immediately — no write.
2. **Get current time** via Bash: `date +%H:%M`. Prepend to the activity line.
3. **Compute today's journal path:** `<graphPath>/journals/yyyy_MM_dd.md` (date with underscores, e.g., `2026_05_01.md`).
4. **If the journal file does not exist:** create it with this content (Write tool):

   ```markdown
   - ## Sessions
   - ## Activity
     - HH:mm {{activity-line}}
   ```

   Then stop.

5. **Locate `## Activity`:** grep the journal for `## Activity` (without leading `- ` — Logseq normalizes `- ## Heading` → `## Heading` when it parses a file; always grep for the heading text without the bullet prefix to match both written and normalized forms). Note the line number.

6. **If `## Activity` heading is absent:** insert it after the `## Sessions` block and append the entry as a child. See step 4 for the insertion format — use the Logseq-normalized form you found (or write the `- ## Activity` form if creating fresh; Logseq will normalize on next parse).

7. **If `## Activity` exists:** append a child bullet at the end of the section, before the next top-level heading or end of file.

   Surgical Edit anchoring: **Read the current state of the section before anchoring** — Logseq may have normalized indents (spaces → tabs) or dropped `- ` from the heading. Use what Read returns as `old_string`, not the original written format. Read the last 3 lines of the `## Activity` section and use that multi-line block as `old_string`; replace with the same block plus the new bullet appended. Use two-space indent if the existing bullets use two-space indent; use tab indent if Logseq has already normalized to tabs.

## Format rules

- Bullet indented two spaces (or tab if Logseq has already normalized) under the `## Activity` heading.
- Prefix each activity bullet with `HH:mm ` from `date +%H:%M`.
- One bullet per call. Multiple calls in the same session each produce a new bullet.

## Side effects

A single Edit (or Write, for first-time creation) on `<graphPath>/journals/yyyy_MM_dd.md`. No reads of the project page, no reads of `pages/Index.md`. Cheap.

## Failure modes

- **Config invalid JSON:** treat `journeyLog` as `true` (default) and log the error to the user, but don't block the main skill.
- **Graph path not resolved:** journey-log is called from skills that have already resolved the path. If the path is missing here, that's a programming error in the calling skill — surface it.
- **Journal write fails (e.g., permission denied):** report the error to the user but don't fail the parent skill. The brain-load itself succeeded; the journey-log entry just didn't land.
