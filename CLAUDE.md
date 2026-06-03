# CLAUDE.md

Personal fork of logseq-brain, packaged as a self-hosted Claude Code plugin.

## Repo layout

```
.claude-plugin/marketplace.json     # marketplace descriptor (name: vlad-brain)
plugins/logseq-brain/               # plugin package — contents of this dir are what gets installed
  .claude-plugin/plugin.json        # plugin metadata (version, author)
  skills/                           # brain-init, brain-load, brain-save, brain-status
  skills/_shared/                   # cross-skill shared references
  CLAUDE.md                         # runtime guidance (Logseq invariants, architecture)
README.md, ROADMAP.md, LICENSE      # repo docs
CONTRIBUTING.md                     # developer notes
```

## Working on skills

- Runtime guidance (Logseq format invariants, shared reference architecture) is in `plugins/logseq-brain/CLAUDE.md`.
- Skills are at `plugins/logseq-brain/skills/<skill-name>/SKILL.md`.
- Shared references are at `plugins/logseq-brain/skills/_shared/<name>.md`.
- The `description` field in each `SKILL.md` frontmatter controls when Claude invokes the skill — edit it carefully.
- No build step. Validation = manual round-trip against a real ClaudeBrain graph (see `CONTRIBUTING.md`).

## Releasing

1. Bump `version` in both `plugins/logseq-brain/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`.
2. Update `ROADMAP.md`.
3. Commit: `git commit -m "chore: prepare vX.Y.Z release"`.
4. Tag and push: `git tag -a vX.Y.Z -m "vX.Y.Z — title" && git push origin vX.Y.Z`.

## Installation

```
/plugin marketplace add vlad-aleksandrov/LogseqBrain
/plugin install logseq-brain@vlad-brain
```
