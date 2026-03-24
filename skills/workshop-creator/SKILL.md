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
- **Sprachen:** Für welche Interface-Sprachen? (Standard: deutsch + english + farsi + arabic)
- **Lektionen:** Wie viele? (Standard: 10, empfohlen: 12)
- **Zielgruppe:** Anfänger / Fortgeschrittene?

Falls der User ein Argument übergibt (z.B. `/workshop-creator Linux`), nutze das als Thema und frage nur nach fehlenden Details.

## Schritt 2 — Workshop-Ordner erstellen

Alle Dateien kommen in diesen Pfad:
```
/Users/reza/Github/openlearnapp/workshops/workshop-[thema]/
```

### Ordnerstruktur (für jede Sprache wiederholen)

```
workshop-[thema]/
├── index.yaml
├── index.html
├── README.md
├── deutsch/
│   ├── workshops.yaml
│   └── [thema]/
│       ├── lessons.yaml
│       ├── thumbnail.svg
│       └── 01-[titel]/content.yaml ... 10-[titel]/content.yaml
├── english/
│   ├── workshops.yaml
│   └── [thema-en]/
│       ├── lessons.yaml
│       └── 01-[title]/content.yaml ...
├── farsi/
│   ├── workshops.yaml
│   └── [thema-fa]/
│       ├── lessons.yaml
│       └── 01-[titel]/content.yaml ...
└── arabic/
    ├── workshops.yaml
    └── [thema-ar]/
        ├── lessons.yaml
        └── 01-[titel]/content.yaml ...
```

## Schritt 3 — Dateien generieren

### index.yaml
```yaml
languages:
  - folder: deutsch
    code: de-DE
  - folder: english
    code: en-US
  - folder: farsi
    code: fa-IR
  - folder: arabic
    code: ar-SA
```

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
- ALLE angegebenen Interface-Sprachen erstellen (q:-Felder gleich, a:/explanation: übersetzt)
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
📚 [N] Lektionen × [M] Sprachen, ca. [X] Beispiele
🎨 color=[...] primaryColor=[...]

→ Sag "importieren" oder nutze /import-workshop um den Workshop live zu schalten.
```

## Was du NICHT tust
- Kein Git, kein Push, kein PR
- Keine Videos oder externe Links
- Keine Platzhalter — jede Datei vollständig
- Nicht mehr als einen Workshop gleichzeitig
