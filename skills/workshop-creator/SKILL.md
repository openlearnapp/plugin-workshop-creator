---
name: workshop-creator
description: Generate complete YAML workshop content for the OpenLearn platform (open-learn.app). Use when creating lessons, workshops, or educational materials for any topic and language.
argument-hint: <topic> [--lang de,en,fa,ar] [--lessons 10]
allowed-tools: Write, Read, Glob, Bash, Grep
---

# Workshop Creator for OpenLearn

Du erstellst vollständige Lernworkshops im YAML-Format für **open-learn.app**.

## Schritt 1 — Thema und Sprachen klären

Frage den User nach:
- **Thema:** Was soll gelehrt werden? (z.B. "Python Grundlagen", "Japanisch", "Erste Hilfe")
- **Sprache:** In welcher Interface-Sprache? (Standard: deutsch — weitere Sprachen später mit `/translate-workshop` hinzufügen)
- **Lektionen:** Wie viele? (Standard: 10, empfohlen: 12)
- **Zielgruppe:** Anfänger / Fortgeschrittene?

Falls der User ein Argument übergibt (z.B. `/workshop-creator Linux`), nutze das als Thema und frage nur nach fehlenden Details.

**Wichtig:** Erstelle den Workshop zunächst in **einer Sprache**. Wenn der Inhalt ausgereift ist, können weitere Sprachen mit `/translate-workshop` hinzugefügt werden.

## Schritt 2 — Workshop-Ordner erstellen

Alle Dateien kommen in diesen Pfad:
```
/Users/reza/Github/openlearnapp/workshops/workshop-[thema]/
```

### Ordnerstruktur (eine Sprache — weitere mit `/translate-workshop` hinzufügen)

```
workshop-[thema]/
├── .gitignore
├── .github/
│   └── workflows/
│       └── static.yml
├── index.yaml
├── index.html
├── README.md
├── CONTRIBUTING.md
├── [sprache]/                  # z.B. deutsch/
│   ├── workshops.yaml
│   └── [thema]/
│       ├── lessons.yaml
│       ├── thumbnail.svg
│       └── 01-[titel]/content.yaml ... N-[titel]/content.yaml
```

### .gitignore (Pflicht — immer erstellen)
```
.DS_Store
*.swp
*~
.vscode/
.idea/
```

### .github/workflows/static.yml (Pflicht — immer erstellen)
```yaml
name: Deploy to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Schritt 3 — Dateien generieren

### index.yaml
```yaml
languages:
  - folder: [sprache]           # z.B. deutsch
    code: [sprach-code]         # z.B. de-DE
```
Weitere Sprachen werden später von `/translate-workshop` hier ergänzt.

### index.html
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta http-equiv="refresh" content="0; url=https://open-learn.app/#/add?source=https://openlearnapp.github.io/workshop-[thema]/">
  <title>[Thema] — Open Learn</title>
</head>
<body><p>Weiterleitung zu Open Learn...</p></body>
</html>
```

### workshops.yaml (pro Sprache)
```yaml
workshops:
  - folder: [thema]
    code: [sprach-code]
    title: "[Anzeigename]"
    description: "[1-2 prägnante Sätze]"
    color: "[H S% L%]"
    primaryColor: "[H S% L%]"
    image: "[thema]/thumbnail.svg"
```

### Farbpalette
| Thema | color | primaryColor |
|-------|-------|-------------|
| Romanische Sprachen | `30 50% 95%` | `25 80% 50%` |
| Arabisch | `150 40% 93%` | `145 65% 38%` |
| Farsi | `270 40% 93%` | `265 60% 48%` |
| Englisch | `220 45% 93%` | `215 70% 45%` |
| Ostasiatische Sprachen | `355 40% 94%` | `350 70% 48%` |
| Mathematik | `200 30% 95%` | `200 65% 40%` |
| Geschichte | `45 40% 94%` | `40 70% 45%` |
| Naturwissenschaft | `140 30% 94%` | `145 60% 38%` |
| Musik | `280 35% 95%` | `275 65% 50%` |
| Technologie/Code | `210 35% 94%` | `210 70% 42%` |

### content.yaml (pro Lektion)
```yaml
version: 2
number: [N]
title: "[Titel]"
description: "[Ein Satz]"
image: "[optionaler Pfad oder URL]"
image_caption: "[optionale Bildunterschrift]"
sections:
  - title: "[Abschnittstitel]"
    video: "[optionale YouTube/Vimeo-URL]"
    image: "[optionaler Pfad oder URL]"
    image_caption: "[optionale Bildunterschrift]"
    explanation: |
      [Markdown mit **fett**, Tabellen, Listen]
    examples:
      - q: "[Frage/Befehl/Quellsatz]"
        a: "[Antwort/Erklärung]"
        image: "[optionaler Pfad oder URL]"
        image_caption: "[optionale Bildunterschrift]"
        labels: ["[Kategorie]"]
        rel:
          - ["[eindeutige-id]", "[bedeutung]", "[kontext]"]

  # Letzte Section = interaktiver Check
  - title: "🖥️ Wissens-Check"
    explanation: |
      Teste dein Wissen!
    examples:
      - q: "[Frage]"
        type: input
        a: "[korrekte Antwort]"
        # Oder mehrere akzeptierte Antworten:
        # a:
        #   - "[Antwort 1]"
        #   - "[Antwort 2]"
      - q: "[Frage]"
        type: select
        options:
          - text: "[Option A]"
            correct: true
          - text: "[Option B]"
          - text: "[Option C]"
          - text: "[Option D]"
      - q: "[Frage]"
        type: multiple-choice
        options:
          - text: "[Option 1]"
            correct: true
          - text: "[Option 2]"
          - text: "[Option 3]"
          - text: "[Option 4]"
            correct: true
```

### CONTRIBUTING.md
Erstelle eine Übersetzungs-Anleitung für Community-Contributors:

```markdown
# Contributing — [Workshop-Name]

## Eine neue Sprache hinzufügen

Dieses Projekt nutzt das [Open Learn](https://open-learn.app) Format. Du kannst eine Übersetzung beitragen, indem du einen bestehenden Sprach-Ordner als Vorlage nutzt.

### Schritt 1: Ordner erstellen

Kopiere einen bestehenden Sprach-Ordner (z.B. `deutsch/`) und benenne ihn um:

| Sprache | Ordner | Code |
|---------|--------|------|
| English | `english` | `en-US` |
| Farsi | `farsi` | `fa-IR` |
| Arabic | `arabic` | `ar-SA` |
| Français | `francais` | `fr-FR` |
| Español | `espanol` | `es-ES` |

### Schritt 2: Inhalte übersetzen

In jeder `content.yaml`:

| Feld | Übersetzen? |
|------|-------------|
| `title`, `description` | ✅ Ja |
| `explanation` | ✅ Ja |
| `a` (Antwort) | ✅ Ja |
| Section `title` | ✅ Ja |
| `options[].text` | ✅ Ja |
| `q` (Frage) | ❌ Nein* |
| `rel` (erster Wert) | ❌ Nein |
| `rel` (zweiter Wert) | ✅ Ja |
| `labels`, `type`, `correct` | ❌ Nein |

*Bei Nicht-Sprach-Workshops (IT, Wissen) werden auch `q`-Felder übersetzt.

### Schritt 3: index.yaml erweitern

Füge deine Sprache in `index.yaml` hinzu:

```yaml
languages:
  - folder: deutsch
    code: de-DE
  - folder: english    # NEU
    code: en-US
```

### Schritt 4: Pull Request erstellen

Erstelle einen PR mit deiner Übersetzung. Bitte prüfe vorher:

- [ ] Alle `content.yaml` Dateien sind gültiges YAML
- [ ] `lessons.yaml` listet alle Lektion-Ordner auf
- [ ] `workshops.yaml` hat `title` und `description` übersetzt
- [ ] `index.yaml` enthält deine neue Sprache
- [ ] Keine `rel`-IDs verändert (erster Wert)
```

### thumbnail.svg
1280×800 SVG mit:
- Farbverlauf-Hintergrund passend zum Workshop
- Thematisches Symbol (SVG-Formen, kein externes Bild)
- Großer Titel-Text
- Untertitel "[N] Lektionen · Open Learn"
- Modern, clean, kein Foto

## Qualitätsregeln

### Pflicht
- Mindestens 10 Lektionen, besser 12 (für Sprach-Workshops: bis zu 50 in 5 Phasen möglich)
- 4–6 Sections pro Lektion (Anfänger: 4–5, Fortgeschrittene: 5–6), 3–5 Examples pro Section
- Letzte Section = interaktiver Check (1× input, 2× select, 1× multiple-choice) mit `correct: true` Markern
- Jede `rel`-ID nur einmal im gesamten Workshop
- Workshop in **einer Sprache** vollständig erstellen (weitere mit `/translate-workshop`)
- `version: 2` in jeder content.yaml
- `description`-Feld in jeder Lektion (wird auf Lesson-Karten angezeigt)

### Bilder und Medien (optional)
- **Lesson-Bild:** `image` + `image_caption` auf Top-Level — wird als Header und Thumbnail angezeigt (min. 640×360, 16:9)
- **Section-Bild:** `image` + `image_caption` auf Section-Ebene — über der Erklärung (min. 800px breit)
- **Example-Bild:** `image` + `image_caption` auf Example-Ebene — Thumbnail neben der Frage
- **Video:** `video`-Feld auf Section-Ebene (YouTube/Vimeo-URL) — wird als Embed angezeigt
- Bilder und Videos sind optional, aber besonders für Schritt-für-Schritt-Anleitungen und visuelle Themen empfohlen

### Coach-Konfiguration (optional)
Falls gewünscht, in `workshops.yaml` ergänzen:
```yaml
coach:
  email: "coach@example.com"
  name: "Coach-Name"
```
Aktiviert den "Ergebnisse per E-Mail senden"-Button auf der Assessment-Seite.

### Labels systematisch einsetzen
- Labels kategorisieren Examples und ermöglichen Filterung
- Für Sprachen: Grammatik-Konzepte (`Präsens`, `Futur`, `Passiv`, `Konjunktiv`, etc.)
- Für IT/Code: Kategorien (`Basics`, `Netzwerk`, `Dateisystem`, `Prozesse`, etc.)
- Für Wissens-Themen: Schwierigkeitsgrad oder Unterthema
- Labels konsistent über alle Lektionen verwenden

### Lernmethoden (immer anwenden)
- **Active Recall:** Situation vor der Frage, nie Antwort in der Frage
- **Emotionaler Anker:** Mind. 1 merkwürdiges/überraschendes Beispiel pro Section
- **Desirable Difficulty:** Fragen bewusst so stellen dass man nachdenken muss
- **Narrative Verankerung:** Roter Faden über alle Lektionen aufbauen
- **Interleaving:** Themen in späteren Lektionen mischen

### Für Sprach-Workshops zusätzlich
- **Verben zuerst:** Die 50 häufigsten Verben mit Konjugationen als Grundlage
- Vokabeln über Verb-Kontexte einführen (nicht isoliert)
- Phase 1 (L1–15): Verb-Grundlagen → Phase 2 (L16–25): Zeiten → Phase 3 (L26–35): Fortgeschrittene Grammatik
- Geschlechter in `rel`-Items angeben: `["Katze", "die Katze (f)", "Substantiv"]`
- Review-Lektionen alle 5 Lektionen einplanen

### Für IT/Code-Workshops zusätzlich
- `q:` = exakter Befehl/Code
- `a:` = was er bewirkt
- Terminal-Check Section mit echten Befehlen zum Eintippen
- `terminal-sim.yaml` pro Lektion erstellen (simulierte Befehle+Ausgaben)

## Schritt 4 — Status aktualisieren

Aktualisiere `/Users/reza/Github/openlearnapp/workshops/workshop-status.md`:

```markdown
### ✅ [DATUM] FERTIG: Workshop [Name]
- **Ordner:** /Users/reza/Github/openlearnapp/workshops/workshop-[name]/
- **Lektionen:** [N] | **Sprachen:** [deutsch, english, farsi, arabic]
- **Interaktiv:** [X] Checks
- **Farben:** color=[...] primaryColor=[...]
- **Schwerpunkte:** [3–4 Stichworte]
- **Status:** BEREIT ZUM IMPORTIEREN
```

## Schritt 5 — Übergabe

Zeige dem User:
```
✅ Workshop [Name] fertig!
📁 /Users/reza/Github/openlearnapp/workshops/workshop-[name]/
📚 [N] Lektionen, [Sprache], ca. [X] Beispiele
🎨 color=[...] primaryColor=[...]

Nächste Schritte:
→ /translate-workshop [name] --lang en    Weitere Sprache hinzufügen
→ /extend-workshop [name] --lessons 11-20 Weitere Lektionen ergänzen
→ /publish-workshop [name]                 Workshop live schalten
```

### Audio-Generierung (optional)
Falls das `generate-audio.sh` Script verfügbar ist:
```bash
cd /Users/reza/Github/openlearnapp/openlearnapp.github.io
./generate-audio.sh /Users/reza/Github/openlearnapp/workshops/workshop-[name]/[sprache]/[thema]/01-[titel]/
```
Voraussetzungen: macOS mit `say`, `yq` (`brew install yq`), `ffmpeg` (`brew install ffmpeg`).

## Was du NICHT tust
- Kein Git, kein Push, kein PR
- Keine Platzhalter — jede Datei vollständig
- Nicht mehr als einen Workshop gleichzeitig
