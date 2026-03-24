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
| **workshop-creator** | `/workshop-creator <topic>` | Create a workshop in one language with lessons, assessments, and thumbnails |
| **translate-workshop** | `/translate-workshop <name> --lang en` | Add a new language to an existing workshop |
| **extend-workshop** | `/extend-workshop <name> --lessons 11-20` | Add more lessons to an existing workshop |
| **validate-workshop** | `/validate-workshop [name]` | Validate structure, YAML, rel-IDs, and schema compliance |
| **publish-workshop** | `/publish-workshop [name]` | Publish to GitHub and deploy via GitHub Pages |
| **check-workshops** | `/check-workshops` | Show status of all workshops |
| **import-workshop** | `/import-workshop` | Import a finished workshop from staging into the project |

## Workflow

```
/workshop-creator       Create workshop in one language
       ↓
/translate-workshop     Add more languages (or let the community contribute)
       ↓
/extend-workshop        Add more lessons over time
       ↓
/validate-workshop      Check structure, schema, rel-IDs
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
- **SVG-Thumbnails** pro Workshop
- **Labels** für Filterung und Kategorisierung
- **CONTRIBUTING.md** für Community-Übersetzungen
- **GitHub Pages Workflow** für automatisches Deployment

### Optionale Features

- **Bilder** auf Lesson-, Section- und Example-Ebene
- **Videos** auf Section-Ebene (YouTube/Vimeo)
- **Coach-Konfiguration** für E-Mail-basiertes Assessment-Feedback
- **Audio-Generierung** via `generate-audio.sh`

## Workshop-Struktur

```
workshop-{topic}/
├── index.yaml                    # Verfügbare Sprachen
├── index.html                    # Redirect zu open-learn.app
├── CONTRIBUTING.md               # Übersetzungs-Anleitung
├── .github/workflows/static.yml  # GitHub Pages Deployment
├── deutsch/                      # Erste Sprache
│   ├── workshops.yaml
│   └── {topic}/
│       ├── lessons.yaml
│       ├── thumbnail.svg
│       └── 01-{title}/content.yaml ... N-{title}/content.yaml
├── english/                      # Später mit /translate-workshop
└── ...                           # Weitere Sprachen
```

## Kompatibilität

- [Open Learn YAML Schema](https://github.com/openlearnapp/openlearnapp.github.io/blob/main/docs/lesson-schema.md) (Version 2)
- [External Workshop Guide](https://github.com/openlearnapp/openlearnapp.github.io/blob/main/docs/external-workshop-guide.md)
- GitHub Pages Deployment

## License

MIT
