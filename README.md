# Workshop Creator Plugin for Claude Code

Claude Code Plugin for the [OpenLearn Platform](https://open-learn.app) — generates complete YAML workshop content in multiple languages using AI.

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
| **workshop-creator** | `/workshop-creator <topic>` | Generate a complete workshop with lessons, assessments, and thumbnails |
| **import-workshop** | `/import-workshop [name]` | Import a finished workshop into GitHub and deploy via GitHub Pages |
| **check-workshops** | `/check-workshops` | Show status of all workshops (local, online, sources) |

## Quick Start

### 1. Workshop erstellen

```
/workshop-creator Linux Grundlagen
```

Claude fragt nach fehlenden Details (Sprachen, Lektionsanzahl, Zielgruppe) und generiert dann den kompletten Workshop.

### 2. Status prüfen

```
/check-workshops
```

Zeigt eine Übersicht aller Workshops: lokal vorhanden, online, Sprachen, Default-Sources.

### 3. Workshop live schalten

```
/import-workshop linux-grundlagen
```

Erstellt ein GitHub-Repo, richtet GitHub Pages ein und deployt den Workshop automatisch.

## Was wird generiert?

Ein vollständiger Workshop mit:

- **10–50 strukturierte Lektionen** (Schema Version 2)
- **4 Interface-Sprachen** (Deutsch, Englisch, Farsi, Arabisch)
- **Interaktive Assessments** mit `correct`-Markern (input, select, multiple-choice)
- **SVG-Thumbnails** pro Workshop
- **Labels** für Filterung und Kategorisierung
- **Terminal-Simulatoren** für IT/Code-Workshops
- **GitHub Pages Workflow** für automatisches Deployment
- **Lernmethoden:** Active Recall, Spaced Repetition, Desirable Difficulty, Narrative Anchoring, Interleaving

### Optionale Features

- **Bilder** auf Lesson-, Section- und Example-Ebene (`image` + `image_caption`)
- **Videos** auf Section-Ebene (YouTube/Vimeo-Einbettung)
- **Coach-Konfiguration** für E-Mail-basiertes Assessment-Feedback

## Workshop-Struktur

```
workshop-{topic}/
├── index.yaml                    # Verfügbare Sprachen
├── index.html                    # Redirect zu open-learn.app
├── .github/workflows/static.yml  # GitHub Pages Deployment
├── deutsch/
│   ├── workshops.yaml            # Metadaten, Farben, Coach
│   └── {topic}/
│       ├── lessons.yaml
│       ├── thumbnail.svg
│       └── 01-{title}/content.yaml ... N-{title}/content.yaml
├── english/
├── farsi/
└── arabic/
```

### content.yaml Format (Version 2)

```yaml
version: 2
number: 1
title: "Lektion 1 — Grundlagen"
description: "Einführung in die wichtigsten Konzepte"
image: "images/header.png"          # Optional: Header-Bild
sections:
  - title: "Abschnitt 1"
    video: "https://youtube.com/watch?v=..."  # Optional
    image: "screenshots/step1.png"            # Optional
    explanation: |
      Markdown-Erklärung mit **Formatierung**.
    examples:
      - q: "Frage oder Aufgabe"
        a: "Antwort oder Erklärung"
        labels: ["Kategorie"]
        rel:
          - ["eindeutige-id", "Bedeutung", "Kontext"]

  - title: "🖥️ Wissens-Check"
    examples:
      - type: select
        q: "Welche Aussage ist korrekt?"
        options:
          - text: "Option A"
            correct: true
          - text: "Option B"
          - text: "Option C"
```

## Angewandte Lernmethoden

| Methode | Umsetzung |
|---------|-----------|
| **Active Recall** | Situation vor der Frage, Antwort nie in der Frage |
| **Emotionale Anker** | Mind. 1 überraschendes Beispiel pro Section |
| **Desirable Difficulty** | Fragen erfordern Nachdenken |
| **Narrative Verankerung** | Roter Faden über alle Lektionen |
| **Interleaving** | Themen werden in späteren Lektionen gemischt |

### Zusätzlich für Sprach-Workshops

- **Verben zuerst:** 50 häufigste Verben als Grundlage
- **5-Phasen-Curriculum:** Verb-Grundlagen → Zeiten → Fortgeschrittene Grammatik → Vokabelerweiterung → Meisterschaft
- **Review-Zyklen:** Alle 5 Lektionen

### Zusätzlich für IT/Code-Workshops

- Exakte Befehle als Fragen, Erklärung als Antwort
- Terminal-Simulatoren mit echten Befehlen
- `terminal-sim.yaml` pro Lektion

## Workflow

```
/workshop-creator  →  Lokaler Workshop in workshops/
       ↓
/check-workshops   →  Status prüfen
       ↓
/import-workshop   →  GitHub Repo + Pages + Default-Sources
       ↓
   open-learn.app  →  Workshop live verfügbar
```

## Kompatibilität

- Generiert Inhalte im [Open Learn YAML Schema](https://github.com/openlearnapp/openlearnapp.github.io/blob/main/docs/lesson-schema.md) (Version 2)
- Kompatibel mit dem [External Workshop Guide](https://github.com/openlearnapp/openlearnapp.github.io/blob/main/docs/external-workshop-guide.md)
- Deployment über GitHub Pages mit automatischem Workflow

## License

MIT
