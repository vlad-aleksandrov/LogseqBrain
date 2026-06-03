# Developer Notes

Personal fork of [jame581/LogseqBrain](https://github.com/jame581/LogseqBrain). Not open for external contributions.

## Project layout

```
.claude-plugin/
  marketplace.json        # marketplace descriptor (name: logseq-brain)
plugins/logseq-brain/
  .claude-plugin/
    plugin.json           # plugin metadata (name, version, author)
  skills/
    brain-init/SKILL.md   # first-time graph setup + new project pages
    brain-load/SKILL.md   # load project context into a session
    brain-save/SKILL.md   # surgical append of logs, decisions, plan updates
    brain-status/SKILL.md # dashboard across all projects
  skills/_shared/
    path-resolution.md    # host-aware graph path resolution
    journey-log.md        # one-line activity-trail write logic
    section-locator.md    # grep-anchored section-targeted reads
    staleness.md          # stale-project rules
  CLAUDE.md               # runtime guidance for Claude (Logseq invariants, architecture)
ROADMAP.md                # shipped / current / future phases
```

## Editing a skill

Skills are YAML-frontmatter markdown files. The `description` field is load-bearing — it controls when Claude invokes the skill.

Keep the body structured around the operations the skill performs. Prefer concrete examples over abstract explanation.

## Logseq format invariants (non-negotiable)

Skills generate content that must round-trip through Logseq's outliner without corruption. Any change to a skill must respect these:

- **Filenames use triple underscore `___` for namespace separators.** `pages/Projects___MyProject.md` renders as `Projects/MyProject` in Logseq.
- **All content must be bullet points.** No bare paragraphs — Logseq is an outliner and swallows them.
- **Properties use `key:: value` on bullet lines** (e.g. `status:: accepted`).
- **Page links use `[[Page Name]]` or `[[Namespace/Page Name]]`.**
- **Dates are always `yyyy-MM-dd`.** Journal filenames use underscores: `journals/yyyy_MM_dd.md`.
- **Writes must be surgical** — use `Edit` to update specific sections, never rewrite whole pages.
- **Logseq normalizes files it has parsed** — `- ## Heading` may become `## Heading`, and space indents may become tabs. Always Read before anchoring an Edit; use the actual format returned, not the originally written format.

## Validation checklist

There is no test suite. Validation is a manual round-trip against a scratch graph.

### Setup

```bash
mkdir -p /tmp/scratch-brain
export LOGSEQ_BRAIN_PATH=/tmp/scratch-brain
```

### Checklist

1. `init brain` — verify `pages/Index.md`, `Meta.md`, `Decisions.md`, `logseq/config.edn`, `journals/.gitkeep` are created.
2. `init brain project ScratchProject` — verify `pages/Projects___ScratchProject.md` is created.
3. `save to brain` — verify session log + decision + Meta updates. Verify `## Activity` gains `HH:mm saved [[Projects/ScratchProject]]`.
4. `load ScratchProject` — verify brief-mode load. Verify `## Activity` gains `HH:mm loaded ... (brief)`.
5. `load ScratchProject full` — verify full-mode load.
6. `brain status` — verify dashboard. Verify `## Activity` gains `HH:mm viewed dashboard`.
7. `what do we know about X` — verify search.
8. **Token check.** Add ~200 lines of fake Session Log. Re-run `load ScratchProject` (brief) and confirm each Read has a small `limit` (~30 lines). No full-file reads.
9. **Surgical edits.** For `brain-save`, confirm the Edit was anchored to a section (no whole-page rewrite).
10. **Config toggle.** Set `~/.config/logseq-brain/config.json` `{"journeyLog": false}`. Re-run any of the above. Verify `## Activity` does NOT gain a new bullet. Restore and verify logging resumes.
11. **gitAutoPush.** Set `"gitAutoPush": true` in config, init the scratch graph as a git repo, run `save to brain`. Verify a commit was created and a push was attempted.

## Releasing

1. Bump `version` in `plugins/logseq-brain/.claude-plugin/plugin.json` AND `.claude-plugin/marketplace.json`.
2. Update `ROADMAP.md`.
3. Commit: `git commit -m "chore: prepare vX.Y.Z release"`.
4. Tag and push: `git tag -a vX.Y.Z -m "vX.Y.Z — title" && git push origin vX.Y.Z`.
