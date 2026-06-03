# Logseq Brain

[![Version](https://img.shields.io/github/v/tag/vlad-aleksandrov/LogseqBrain?label=version&color=blue)](https://github.com/vlad-aleksandrov/LogseqBrain/releases)
[![License](https://img.shields.io/github/license/vlad-aleksandrov/LogseqBrain?color=green)](./LICENSE)
[![Fork of](https://img.shields.io/badge/fork%20of-jame581%2FLogseqBrain-grey)](https://github.com/jame581/LogseqBrain)

Persistent memory for Claude using a dedicated [Logseq](https://logseq.com) graph. Save and load project context, decisions, and progress across sessions and devices.

## Overview

This plugin turns a Logseq graph into Claude's external brain. Claude can read from and write to the graph, storing project plans, decisions, implementation details, and session logs. Because Logseq syncs across devices, your Claude context travels with you — start a task on your desktop, continue on your notebook.

## Install

### Claude Code

```
/plugin marketplace add vlad-aleksandrov/LogseqBrain
/plugin install logseq-brain@vlad-brain
```

## Setup

After installing, create a Logseq graph (e.g. "ClaudeBrain") and tell the plugin where to find it. Pick one:

- **Environment variable**: `export LOGSEQ_BRAIN_PATH=/path/to/ClaudeBrain`
- **Config file**: create `~/.config/logseq-brain/config.json`:
  ```bash
  mkdir -p ~/.config/logseq-brain
  echo '{"graphPath": "/path/to/ClaudeBrain"}' > ~/.config/logseq-brain/config.json
  ```
- **Just tell Claude the path** when prompted

To enable automatic git push after each save, add `"gitAutoPush": true` to the config file.

Then say **"init brain"** to set up the graph structure, and **"init brain project MyProject"** to add your first project.

## Skills

**brain-init** — Set up the graph for the first time, or add a new project.
- "init brain" — creates the graph structure (Index, Meta, Decisions pages)
- "init brain project MyProject" — adds a new project page

**brain-load** — Load project context into the current session.
- "load MyProject" — loads the project context (brief mode by default)
- "load MyProject full" — loads everything including decisions, implementation, linked tasks
- "load brain" — loads a high-level overview of all projects
- "what do we know about strategy pattern" — searches across the graph

**brain-save** — Save the current session's work to the graph.
- "save to brain" — saves decisions, progress, and plans from this session
- "save progress" — same as above
- "remember this" — save specific information
- Automatically detects multi-project sessions and Jira task context
- Updates Meta.md when new user preferences are discovered

**brain-status** — Quick dashboard of all projects.
- "brain status" — shows all projects with status, last activity, current focus
- "show projects" — same as above
- Flags stale projects that haven't been updated recently

## Graph Structure

```
ClaudeBrain/
├── pages/
│   ├── Index.md                    ← master index
│   ├── Meta.md                     ← your preferences and conventions
│   ├── Decisions.md                ← cross-project decisions
│   └── Projects___MyProject.md     ← project pages (namespace: Projects/)
├── journals/
│   └── 2026_04_12.md                ← daily journal: ## Sessions + ## Activity
└── logseq/
    └── config.edn                   ← Logseq graph config
```

## Journey Log

Every brain operation (init / load / save / status / search) leaves a one-line `HH:mm` timestamped bullet in today's journal under `## Activity` — a low-cost audit trail of what Claude did, when. Disable by adding `"journeyLog": false` to `~/.config/logseq-brain/config.json`.
