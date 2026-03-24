# Workshop Creator Plugin for Claude Code

Claude Code Plugin for the [OpenLearn Platform](https://open-learn.app) — generates complete YAML workshop content using AI.

## Installation

In Claude Code, install the plugin:
```
/install-plugin https://github.com/openlearnapp/plugin-workshop-creator
```

Or clone into your project:
```bash
git clone https://github.com/openlearnapp/plugin-workshop-creator .claude/plugins/workshop-creator
```

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| **workshop-creator** | `/workshop-creator <topic>` | Create a workshop in one language with lessons, assessments, thumbnails, and diagrams |
| **translate-workshop** | `/translate-workshop <name> --lang en` | Add a new language to an existing workshop |
| **extend-workshop** | `/extend-workshop <name> --lessons 11-20` | Add more lessons to an existing workshop |
| **update-workshop** | `/update-workshop <name> [--dry-run]` | Idempotent update — fix structure, migrate schema, add missing media |
| **validate-workshop** | `/validate-workshop [name]` | Validate structure, YAML, rel-IDs, schema, README links, and media |
| **publish-workshop** | `/publish-workshop [name]` | Publish to GitHub, deploy via GitHub Pages, create Pages setup issue |
| **check-workshops** | `/check-workshops` | Show status of all workshops |
| **import-workshop** | `/import-workshop` | Import a finished workshop from staging into the project |

## Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│  Claude Code (CLI)                                                  │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  Workshop Plugin                                              │  │
│  │                                                               │  │
│  │  /workshop-creator  → Create YAML lessons, thumbnails, SVGs   │  │
│  │  /translate-workshop → Add languages                          │  │
│  │  /extend-workshop    → Add more lessons                       │  │
│  │  /update-workshop    → Fix & upgrade existing workshops       │  │
│  │  /validate-workshop  → Check schema & quality                 │  │
│  │  /publish-workshop   → Push to GitHub + enable Pages          │  │
│  └──────────────────────────────┬────────────────────────────────┘  │
└─────────────────────────────────┼───────────────────────────────────┘
                                  │ generates
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│  Workshop Repository (e.g. workshop-docker)                         │
│                                                                     │
│  index.yaml ─── deutsch/workshops.yaml ─── docker/lessons.yaml      │
│                                              ├── 01-*/content.yaml  │
│                                              ├── 02-*/content.yaml  │
│                                              └── thumbnail.svg      │
│  + README.md, CONTRIBUTING.md, .github/workflows/static.yml         │
└─────────────────────────────────┬───────────────────────────────────┘
                                  │ git push
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│  GitHub Pages  (static hosting)                                     │
│                                                                     │
│  https://open-learn.app/workshop-docker/                            │
│  ├── index.yaml              ← entry point for the platform        │
│  ├── index.html              ← redirect to open-learn.app          │
│  └── deutsch/docker/01-*/content.yaml                               │
└─────────────────────────────────┬───────────────────────────────────┘
                                  │ fetches YAML at runtime
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│  Open Learn Platform  (https://open-learn.app)                      │
│                                                                     │
│  Vue.js SPA that loads workshops from any static URL:               │
│                                                                     │
│  1. Learner clicks share link or visits landing page                │
│  2. App fetches index.yaml → discovers languages                    │
│  3. App fetches workshops.yaml → discovers workshops                │
│  4. App fetches lessons.yaml → loads lesson list                    │
│  5. App fetches content.yaml → renders lessons with:                │
│     • Interactive assessments (input, select, multiple-choice)      │
│     • Vocabulary tracking (rel-IDs across lessons)                  │
│     • Audio playback (MP3 per example)                              │
│     • Labels for filtering                                          │
│     • Progress saved in browser (localStorage)                      │
└─────────────────────────────────────────────────────────────────────┘
```

### How it connects

**Claude Code + Plugin** is the authoring tool. The plugin's skills generate complete YAML workshop content — lessons, assessments, thumbnails, diagrams — following the Open Learn schema. Everything stays local until you run `/publish-workshop`.

**Workshop Repositories** are the output. Each workshop is a standalone GitHub repo with YAML files, SVG assets, and a GitHub Actions workflow for deployment. The repo is self-contained — no build step, no dependencies.

**GitHub Pages** serves the raw YAML files as a static site. The `index.html` at the root redirects visitors to the platform. The custom domain `open-learn.app` maps to the GitHub Pages sites, so `open-learn.app/workshop-docker/` serves the Docker workshop files.

**Open Learn Platform** is the learner-facing app. It never bundles workshop content — instead it fetches YAML files at runtime from any URL. When a learner clicks a share link like `open-learn.app/#/add?source=open-learn.app/workshop-docker/`, the app discovers and loads the entire workshop. This decoupled architecture means workshops can be hosted anywhere (GitHub Pages, IPFS, custom servers) and the platform picks them up automatically.

**The full cycle:**
```
Author writes → Plugin generates YAML → GitHub hosts files → Platform loads at runtime → Learner studies
         ↑                                                                                    │
         └── /update-workshop can re-run on any existing workshop (idempotent) ───────────────┘
```

## Workflow

```
/workshop-creator       Create workshop in one language
       ↓
/translate-workshop     Add more languages (or let the community contribute)
       ↓
/extend-workshop        Add more lessons over time
       ↓
/update-workshop        Fix structure, migrate schema, add missing media (idempotent)
       ↓
/validate-workshop      Check structure, schema, rel-IDs, README links, media
       ↓
/check-workshops        Review status across all workshops
       ↓
/publish-workshop       Deploy to GitHub Pages → live on open-learn.app
```

## Quick Start

### 1. Workshop erstellen (eine Sprache)

```
/workshop-creator Linux Grundlagen
```

Claude erstellt den Workshop zunächst in **einer Sprache**. So kann der Inhalt erst ausreifen, bevor weitere Sprachen hinzukommen.

### 2. Weitere Sprache hinzufügen

```
/translate-workshop linux-grundlagen --lang en
```

Übersetzt alle Lektionen: `a`-Felder, `explanation`, `title`, `description`. Die `q`-Felder und `rel`-IDs bleiben konsistent.

### 3. Workshop erweitern

```
/extend-workshop linux-grundlagen --lessons 11-20
```

Fügt neue Lektionen hinzu, ohne bestehende zu verändern. Kennt alle bisherigen `rel`-IDs und baut inhaltlich darauf auf.

### 4. Workshop veröffentlichen

```
/publish-workshop linux-grundlagen
```

Erstellt GitHub-Repo, GitHub Pages Workflow, und aktualisiert Default-Sources.

## Community-Übersetzungen

Jeder Workshop enthält eine `CONTRIBUTING.md` mit Anleitung für Contributors. Eine neue Sprache hinzuzufügen ist einfach:

1. Sprach-Ordner kopieren und umbenennen
2. Markierte Felder übersetzen (`a`, `explanation`, `title`, `description`)
3. `index.yaml` um die neue Sprache erweitern
4. Pull Request erstellen

Felder die **nicht** übersetzt werden: `q` (bei Sprach-Workshops), `rel`-IDs, `labels`, `type`, `correct`.

## Was wird generiert?

- **10–50 strukturierte Lektionen** (Schema Version 2)
- **Interaktive Assessments** mit `correct`-Markern (input, select, multiple-choice)
- **SVG-Thumbnails** pro Workshop (thematische Symbole, Farbverlauf)
- **SVG-Diagramme** als Lesson-Bilder (IT/Wissenschaft: Architektur, Abläufe, Topologien)
- **README.md** mit Landing Page + Start Workshop Links
- **Labels** für Filterung und Kategorisierung
- **CONTRIBUTING.md** für Community-Übersetzungen
- **GitHub Pages Workflow** für automatisches Deployment

### Optionale Features

- **terminal-sim.yaml** für IT/Code-Workshops (simulierte Terminal-Befehle)
- **Bilder** auf Lesson-, Section- und Example-Ebene
- **Videos** auf Section-Ebene (YouTube/Vimeo)
- **Coach-Konfiguration** für E-Mail-basiertes Assessment-Feedback
- **Audio-Generierung** via `generate-audio.sh`

## Workshop-Struktur

```
workshop-{topic}/
├── index.yaml                    # Verfügbare Sprachen
├── index.html                    # Redirect zu open-learn.app
├── README.md                     # Landing Page + Start Workshop Links
├── CONTRIBUTING.md               # Übersetzungs-Anleitung
├── .github/workflows/static.yml  # GitHub Pages Deployment
├── deutsch/                      # Erste Sprache
│   ├── workshops.yaml
│   └── {topic}/
│       ├── lessons.yaml
│       ├── thumbnail.svg         # 1280×800 Workshop-Thumbnail
│       ├── 01-{title}/
│       │   ├── content.yaml
│       │   └── diagram.svg       # Optional: Lesson-Diagramm
│       └── N-{title}/content.yaml
├── english/                      # Später mit /translate-workshop
└── ...                           # Weitere Sprachen
```

## Kompatibilität

- [Open Learn YAML Schema](https://github.com/openlearnapp/openlearnapp.github.io/blob/main/docs/lesson-schema.md) (Version 2)
- [External Workshop Guide](https://github.com/openlearnapp/openlearnapp.github.io/blob/main/docs/external-workshop-guide.md)
- GitHub Pages Deployment

## License

MIT
