---
name: workshop-creator
description: Generate complete YAML workshop content for the OpenLearn platform (open-learn.app). Use when creating lessons, workshops, or educational materials for any topic and language.
argument-hint: <topic> [--lang de,en,fa,ar] [--lessons 10]
allowed-tools: Write, Read, Glob, Bash, Grep
---

# Workshop Creator for OpenLearn

Du erstellst vollständige Lernworkshops im YAML-Format für **open-learn.app**.

## Schritt 1 — Thema, Typ und Sprachen klären

Frage den User nach:
- **Typ:** Lern-Workshop (Wissen, Sprachen, IT) oder **Story-Workshop** (interaktive Geschichte mit Entscheidungen)?
- **Thema:** Was soll gelehrt/erzählt werden? (z.B. "Python Grundlagen", "Japanisch", "Milas Abenteuer")
- **Sprache:** In welcher Interface-Sprache? (Standard: deutsch — weitere Sprachen später mit `/translate-workshop` hinzufügen)
- **Lektionen:** Wie viele? (Lern-Workshop: Standard 10, empfohlen 12 — Story-Workshop: je nach Verzweigungstiefe, min. 3)
- **Zielgruppe:** Anfänger / Fortgeschrittene? (für Lern-Workshops) / Altersgruppe? (für Story-Workshops)

Falls der User ein Argument übergibt (z.B. `/workshop-creator Linux`), nutze das als Thema und frage nur nach fehlenden Details.

**Wichtig:** Erstelle den Workshop zunächst in **einer Sprache**. Wenn der Inhalt ausgereift ist, können weitere Sprachen mit `/translate-workshop` hinzugefügt werden.

Bei **Story-Workshops**: Springe zu [Story Mode](#story-mode-workshops) am Ende dieser Skill-Datei.

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
├── README.md                   # Mit Landing Page + Start Workshop Links
├── CONTRIBUTING.md
├── [sprache]/                  # z.B. deutsch/
│   ├── workshops.yaml
│   └── [thema]/
│       ├── lessons.yaml
│       ├── thumbnail.svg       # 1280×800 Workshop-Thumbnail
│       ├── 01-[titel]/
│       │   ├── content.yaml
│       │   └── images/
│       │       ├── lesson-header.svg              # PFLICHT: Lesson-Bild (640×360)
│       │       ├── section-01-[kurzname].svg      # PFLICHT: Section-Bild (640×160)
│       │       ├── section-02-[kurzname].svg
│       │       └── section-0N-[kurzname].svg
│       ├── 02-[titel]/
│       │   ├── content.yaml
│       │   └── images/
│       │       ├── lesson-header.svg
│       │       └── section-*.svg
│       └── N-[titel]/
│           ├── content.yaml
│           └── images/
│               ├── lesson-header.svg
│               └── section-*.svg
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
  <meta http-equiv="refresh" content="0; url=https://open-learn.app/#/add?source=https://open-learn.app/workshop-[thema]/">
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
    image: "[thema]/thumbnail.svg"    # PFLICHT — ohne image kein Bild auf der Übersichtsseite!
```

**PFLICHT:** Das `image`-Feld darf NIEMALS fehlen. Ohne `image` wird der Workshop auf der Übersichtsseite ohne Bild angezeigt.

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
image: "images/lesson-header.svg"
image_caption: "[Beschreibung des Bildes]"
sections:
  - title: "[Abschnittstitel]"
    video: "[optionale YouTube/Vimeo-URL]"
    image: "images/section-01-[kurzname].svg"
    image_caption: "[Beschreibung — was zeigt das Bild]"
    explanation: |
      [Markdown mit **fett**, Tabellen, Listen]
    examples:
      - q: "[Frage/Befehl/Quellsatz]"
        a: "[Antwort/Erklärung]"
        labels: ["[Kategorie1]", "[Kategorie2]"]
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
        labels: ["[Kategorie]"]
        options:
          - text: "[Option A]"
            correct: true
          - text: "[Option B]"
          - text: "[Option C]"
          - text: "[Option D]"
      - q: "[Frage]"
        type: multiple-choice
        labels: ["[Kategorie]"]
        options:
          - text: "[Option 1]"
            correct: true
          - text: "[Option 2]"
          - text: "[Option 3]"
          - text: "[Option 4]"
            correct: true
```

**ACHTUNG — Assessment-Format:**
- Options MÜSSEN als Objekte mit `text` und `correct` geschrieben werden
- NIEMALS einfache Strings als Options verwenden (z.B. `- "Option A"` + separates `a:` Feld)
- Das veraltete Format (`options: ["string"]` + `a: "string"`) zeigt in der App **leere Radio-Buttons/Checkboxen** ohne sichtbaren Text!
- Assessment-Examples brauchen auch `labels`

### README.md (Pflicht — immer erstellen)
```markdown
# Workshop: [Thema-Titel]

> **[Landing Page](https://open-learn.app/workshop-[thema]/)** · **[Start Workshop](https://open-learn.app/#/add?source=https://open-learn.app/workshop-[thema]/)**

[1-2 Sätze Beschreibung] für [open-learn.app](https://open-learn.app).

## Inhalt
- [N] Lektionen ([Zielgruppe])
- [M] Sprachen: [Liste]
- Interaktive Wissens-Checks in jeder Lektion

## Lektionen
1. **[Titel]** — [Kurzbeschreibung]
2. **[Titel]** — [Kurzbeschreibung]
...

## Sprachen
- [Liste der verfügbaren Sprachen mit Interface-Angabe]

## Links
- [Open Learn Platform](https://open-learn.app)
- [OpenLearn GitHub](https://github.com/openlearnapp)
```

**Wichtig:** Die Links im `> **[Landing Page]...`-Block müssen exakt dem Schema entsprechen:
- Landing Page: `https://open-learn.app/workshop-[thema]/`
- Start Workshop: `https://open-learn.app/#/add?source=https://open-learn.app/workshop-[thema]/`

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

### thumbnail.svg (Pflicht — mit Write-Tool erstellen!)
Erstelle die Datei `[sprache]/[thema]/thumbnail.svg` mit dem **Write-Tool**. 1280×800 SVG mit:
- Farbverlauf-Hintergrund passend zur `primaryColor` des Workshops
- Thematisches Symbol als SVG-Pfade (kein externes Bild, kein `<image>`-Tag)
- Großer Titel-Text (Workshop-Titel in der jeweiligen Sprache)
- Untertitel "[N] Lektionen · Open Learn"
- Modern, clean, kein Foto
- OPEN LEARN Branding unten

**Beispiel-Vorlage:**
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1280 800">
  <defs>
    <linearGradient id="bg" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" stop-color="[helle Farbe]"/>
      <stop offset="100%" stop-color="[dunkle Farbe]"/>
    </linearGradient>
  </defs>
  <rect width="1280" height="800" fill="url(#bg)" rx="24"/>
  <rect x="240" y="120" width="800" height="560" fill="white" fill-opacity="0.95" rx="32"/>
  <!-- Thematisches Symbol als SVG-Pfade hier -->
  <text x="640" y="420" text-anchor="middle" font-family="system-ui,sans-serif"
        font-size="56" font-weight="700" fill="[primaryColor]">[Workshop-Titel]</text>
  <text x="640" y="480" text-anchor="middle" font-family="system-ui,sans-serif"
        font-size="28" fill="#64748b">[N] Lektionen · Open Learn</text>
</svg>
```

Das thematische Symbol soll zum Workshop-Thema passen:
- **IT/Code:** Terminal-Icon, Zahnräder, Code-Klammern, Server-Rack
- **Sprachen:** Sprechblasen, Flaggen-Formen, Buchstaben der Zielsprache
- **Wissenschaft:** Atome, Reagenzgläser, DNA-Helix
- **Musik:** Noten, Notenschlüssel, Wellenformen
- **Geschichte:** Bücher, Zeitleisten, Gebäude-Silhouetten

### Lesson-Bilder generieren (PFLICHT — für jede Lektion)

**Jede Lektion braucht ein Bild.** Ohne Bild sieht die Lektionskarte im Lernpfad leer aus.

#### Schritt-für-Schritt für JEDE Lektion:

1. **Erstelle den Ordner** `images/` im Lektion-Ordner (per Bash: `mkdir -p ...`)
2. **Schreibe die SVG-Datei** mit dem Write-Tool nach `images/lesson-header.svg`
3. **Setze in `content.yaml`:** `image: "images/lesson-header.svg"` und `image_caption: "[Beschreibung]"`

**Du musst die SVG-Datei tatsächlich mit dem Write-Tool erstellen!** Nicht nur den Pfad in content.yaml referenzieren — die Datei muss existieren.

#### Dateistruktur pro Lektion:
```
01-[titel]/
├── content.yaml                    ← enthält image: "images/lesson-header.svg"
└── images/
    └── lesson-header.svg           ← DIESE DATEI MUSST DU ERSTELLEN (Write-Tool)
```

#### SVG-Spezifikation:
- **Format:** 640×270 Pixel (Hero-Stil) — wird in der App `w-full` angezeigt und skaliert proportional
- **Stil:** Educational Hero — großes Emoji/Icon, Titel, Untertitel, 3 animierte Feature-Karten
- **Dark-Mode-tauglich:** Dunkler Hintergrund `#0d1117`, Header-Leiste `#161b22`
- **Inhalt:** Übersicht der Lektion — Emoji, Lesson-Titel, Subtitle mit Kernbefehlen, 3 Karten für Hauptthemen
- **Keine externen Bilder:** Nur SVG-Pfade, Formen, Text (kein `<image>`-Tag)
- **Sprach-spezifisch:** Jede Sprache hat eigene übersetzten SVGs — Bilder werden NICHT über Sprachen geteilt
- **Animation:** Sequentielles Einblenden (title → subtitle → Karte 1 → Karte 2 → Karte 3 → Footer), 11–13s Loop

#### SVG-Vorlage (Lesson-Header Hero-Stil):
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 270" width="640" height="270">
<style>
  .f { font-family: sans-serif; }
  .ti { animation: tia 13s infinite; }
  @keyframes tia { 0%,3%{opacity:0} 7%,87%{opacity:1} 92%,100%{opacity:0} }
  .sub { animation: suba 13s infinite; }
  @keyframes suba { 0%,10%{opacity:0} 14%,87%{opacity:1} 92%,100%{opacity:0} }
  .c1 { animation: c1a 13s infinite; }
  @keyframes c1a { 0%,18%{opacity:0} 24%,87%{opacity:1} 92%,100%{opacity:0} }
  .c2 { animation: c2a 13s infinite; }
  @keyframes c2a { 0%,38%{opacity:0} 44%,87%{opacity:1} 92%,100%{opacity:0} }
  .c3 { animation: c3a 13s infinite; }
  @keyframes c3a { 0%,56%{opacity:0} 62%,87%{opacity:1} 92%,100%{opacity:0} }
  .ft { animation: fta 13s infinite; }
  @keyframes fta { 0%,72%{opacity:0} 76%,87%{opacity:1} 92%,100%{opacity:0} }
</style>
<rect width="640" height="270" rx="12" fill="#0d1117"/>
<rect width="640" height="38" rx="12" fill="#161b22"/>
<rect y="26" width="640" height="12" fill="#161b22"/>
<!-- Großes Emoji-Icon -->
<text x="320" y="90" text-anchor="middle" font-size="44">[EMOJI]</text>
<!-- Titel -->
<text class="f ti" x="320" y="125" text-anchor="middle" font-size="20" fill="#e6edf3" font-weight="bold">[Lektion-Titel]</text>
<!-- Untertitel mit Kernbefehlen -->
<text class="f sub" x="320" y="148" text-anchor="middle" font-size="11" fill="#6e7681">[Befehl1] · [Befehl2] · [Befehl3]</text>
<!-- 3 Feature-Karten -->
<g class="c1">
  <rect x="20" y="162" width="185" height="72" rx="8" fill="#0d1520" stroke="#58a6ff" stroke-width="1.5"/>
  <text x="112" y="192" text-anchor="middle" font-size="22">[EMOJI1]</text>
  <text class="f" x="112" y="212" text-anchor="middle" font-size="11" fill="#58a6ff" font-weight="bold">[Thema 1]</text>
  <text class="f" x="112" y="227" text-anchor="middle" font-size="9" fill="#8b949e">[Kurzbeschreibung]</text>
</g>
<g class="c2">
  <rect x="227" y="162" width="185" height="72" rx="8" fill="#0a1a0d" stroke="#3fb950" stroke-width="1.5"/>
  <text x="319" y="192" text-anchor="middle" font-size="22">[EMOJI2]</text>
  <text class="f" x="319" y="212" text-anchor="middle" font-size="11" fill="#3fb950" font-weight="bold">[Thema 2]</text>
  <text class="f" x="319" y="227" text-anchor="middle" font-size="9" fill="#8b949e">[Kurzbeschreibung]</text>
</g>
<g class="c3">
  <rect x="434" y="162" width="185" height="72" rx="8" fill="#1a0f08" stroke="#f0883e" stroke-width="1.5"/>
  <text x="526" y="192" text-anchor="middle" font-size="22">[EMOJI3]</text>
  <text class="f" x="526" y="212" text-anchor="middle" font-size="11" fill="#f0883e" font-weight="bold">[Thema 3]</text>
  <text class="f" x="526" y="227" text-anchor="middle" font-size="9" fill="#8b949e">[Kurzbeschreibung]</text>
</g>
<text class="f ft" x="320" y="258" text-anchor="middle" font-size="9" fill="#6e7681">[Footer-Tipp]</text>
</svg>
```

#### Bild-Inhalt je nach Workshop-Typ:

| Workshop-Typ | Was zeichnen? | Beispiele |
|-------------|---------------|-----------|
| IT/Code | Architektur, Terminal, Netzwerk, Ablauf | Docker-Container-Stack, CLI-Fenster, Server-Diagramm |
| Sprachen | Szenen, Konversation, Objekte | Café mit Sprechblasen, Marktstand, Reisekoffer |
| Wissenschaft | Schema, Prozess, Modell | Atom-Aufbau, DNA-Helix, Periodensystem-Ausschnitt |
| Geschichte | Timeline, Karte, Gebäude | Epochen-Leiste, Weltkarte-Ausschnitt, Tempel-Silhouette |
| Musik | Noten, Akkorde, Wellen | Notensystem mit Melodie, Gitarren-Griffbrett |

### Section-Bilder generieren (PFLICHT — für JEDE Section)

**Jede Section braucht ein Bild.** Section-Bilder machen Lektionen visuell ansprechend und merkbar. Lerner erinnern sich an Inhalte besser, wenn sie visuelle Anker haben (Dual Coding Theory).

#### Schritt-für-Schritt für JEDE Section:

1. **Erstelle die SVG-Datei** mit dem Write-Tool nach `images/section-[nr]-[kurzname].svg` (z.B. `images/section-01-terminal.svg`)
2. **Setze in `content.yaml`:** `image: "images/section-01-terminal.svg"` und `image_caption: "[Beschreibung]"` auf der Section

#### SVG-Spezifikation für Section-Bilder:
- **Format:** 640×210 (3 Karten) oder 640×230 (4 Karten) — wird `w-full` angezeigt
- **Stil:** Educational Infographic — Karten mit farbigen Borders, Emojis, Sans-serif — KEIN Terminal-Fenster, KEINE macOS-Dots
- **Dark-Mode-tauglich:** Hintergrund `#0d1117`, Header-Leiste `#161b22`
- **Inhalt:** Erklärt den Section-Inhalt mit 3–4 Karten (je eine Karte pro Kernkonzept)
- **Keine externen Bilder:** Nur SVG-Pfade, Formen, Text (kein `<image>`-Tag)
- **Sprach-spezifisch:** Text in der Sprache des Workshops — NICHT sprachübergreifend teilen
- **Animation:** Sequentielles Einblenden der Karten (c1 → c2 → c3 → c4), 11s Loop
- **Schrift:** `font-family: sans-serif` — NIEMALS monospace für Karten-Labels

#### Farbpalette:
- Hintergrund: `#0d1117`, Header: `#161b22`
- Blau: `#58a6ff` (Info), Grün: `#3fb950` (Erfolg/Empfehlung), Orange: `#f0883e` (Warnung)
- Rot: `#f85149` (Löschen/Gefahr), Lila: `#bc8cff` (Special), Grau: `#8b949e` (Subtext)
- Karten-Hintergrund: `#0d1520` (blau), `#0a1a0d` (grün), `#1a0f08` (orange), `#180a0a` (rot)

#### SVG-Vorlage für Section-Bilder (Educational Infographic — 4 Karten):
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 230" width="640" height="230">
<style>
  .f { font-family: sans-serif; }
  .ti { animation: tia 11s infinite; }
  @keyframes tia { 0%,3%{opacity:0} 7%,87%{opacity:1} 92%,100%{opacity:0} }
  .c1 { animation: c1a 11s infinite; }
  @keyframes c1a { 0%,12%{opacity:0} 18%,87%{opacity:1} 92%,100%{opacity:0} }
  .c2 { animation: c2a 11s infinite; }
  @keyframes c2a { 0%,30%{opacity:0} 36%,87%{opacity:1} 92%,100%{opacity:0} }
  .c3 { animation: c3a 11s infinite; }
  @keyframes c3a { 0%,48%{opacity:0} 54%,87%{opacity:1} 92%,100%{opacity:0} }
  .c4 { animation: c4a 11s infinite; }
  @keyframes c4a { 0%,65%{opacity:0} 71%,87%{opacity:1} 92%,100%{opacity:0} }
  .ft { animation: fta 11s infinite; }
  @keyframes fta { 0%,78%{opacity:0} 82%,87%{opacity:1} 92%,100%{opacity:0} }
</style>
<rect width="640" height="230" rx="12" fill="#0d1117"/>
<rect width="640" height="38" rx="12" fill="#161b22"/>
<rect y="26" width="640" height="12" fill="#161b22"/>
<text class="f ti" x="320" y="24" text-anchor="middle" font-size="13" fill="#e6edf3" font-weight="bold" letter-spacing="1">[Section-Titel]</text>
<!-- Karte 1 -->
<g class="c1">
  <rect x="16" y="46" width="140" height="158" rx="10" fill="#0d1520" stroke="#58a6ff" stroke-width="2"/>
  <text x="86" y="88" text-anchor="middle" font-size="26" fill="#58a6ff">[EMOJI]</text>
  <text class="f" x="86" y="112" text-anchor="middle" font-size="11" fill="#58a6ff" font-weight="bold">[Titel]</text>
  <text class="f" x="86" y="130" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 1]</text>
  <text class="f" x="86" y="145" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 2]</text>
</g>
<!-- Karte 2 -->
<g class="c2">
  <rect x="168" y="46" width="140" height="158" rx="10" fill="#0a1a0d" stroke="#3fb950" stroke-width="2"/>
  <text x="238" y="88" text-anchor="middle" font-size="26" fill="#3fb950">[EMOJI]</text>
  <text class="f" x="238" y="112" text-anchor="middle" font-size="11" fill="#3fb950" font-weight="bold">[Titel]</text>
  <text class="f" x="238" y="130" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 1]</text>
  <text class="f" x="238" y="145" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 2]</text>
</g>
<!-- Karte 3 -->
<g class="c3">
  <rect x="320" y="46" width="140" height="158" rx="10" fill="#1a0f08" stroke="#f0883e" stroke-width="2"/>
  <text x="390" y="88" text-anchor="middle" font-size="26" fill="#f0883e">[EMOJI]</text>
  <text class="f" x="390" y="112" text-anchor="middle" font-size="11" fill="#f0883e" font-weight="bold">[Titel]</text>
  <text class="f" x="390" y="130" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 1]</text>
  <text class="f" x="390" y="145" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 2]</text>
</g>
<!-- Karte 4 -->
<g class="c4">
  <rect x="472" y="46" width="152" height="158" rx="10" fill="#180a0a" stroke="#f85149" stroke-width="2"/>
  <text x="548" y="88" text-anchor="middle" font-size="26" fill="#f85149">[EMOJI]</text>
  <text class="f" x="548" y="112" text-anchor="middle" font-size="11" fill="#f85149" font-weight="bold">[Titel]</text>
  <text class="f" x="548" y="130" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 1]</text>
  <text class="f" x="548" y="145" text-anchor="middle" font-size="9" fill="#8b949e">[Zeile 2]</text>
</g>
<text class="f ft" x="320" y="223" text-anchor="middle" font-size="9" fill="#6e7681">[Footer-Tipp]</text>
</svg>
```

#### Dateistruktur pro Lektion (mit Section-Bildern):
```
01-[titel]/
├── content.yaml
└── images/
    ├── lesson-header.svg              ← Lesson-Bild (640×270, Hero-Stil)
    ├── section-01-[kurzname].svg      ← Section 1 Bild (640×210-230, Infographic)
    ├── section-02-[kurzname].svg      ← Section 2 Bild (640×210-230, Infographic)
    ├── section-03-[kurzname].svg      ← Section 3 Bild (640×210-230, Infographic)
    └── section-04-[kurzname].svg      ← Section 4 Bild (640×210-230, Infographic)
```

#### Beispiel content.yaml mit Section-Bildern:
```yaml
sections:
  - title: "Docker-Architektur"
    image: "images/section-01-architektur.svg"
    image_caption: "Docker Engine, Images und Container im Zusammenspiel"
    explanation: |
      Docker besteht aus drei Hauptkomponenten...
```

**Du musst die SVG-Dateien tatsächlich mit dem Write-Tool erstellen!** Nicht nur den Pfad in content.yaml referenzieren.

## Qualitätsregeln

### Pflicht
- Mindestens 10 Lektionen, besser 12 (für Sprach-Workshops: bis zu 50 in 5 Phasen möglich)
- 4–6 Sections pro Lektion (Anfänger: 4–5, Fortgeschrittene: 5–6), 3–5 Examples pro Section
- Letzte Section = interaktiver Check (1× input, 2× select, 1× multiple-choice) mit `correct: true` Markern
- Jede `rel`-ID nur einmal im gesamten Workshop
- Workshop in **einer Sprache** vollständig erstellen (weitere mit `/translate-workshop`)
- `version: 2` in jeder content.yaml
- `description`-Feld in jeder Lektion (wird auf Lesson-Karten angezeigt)
- **`image`-Feld in JEDER Lektion** — `image: "images/lesson-header.svg"` + SVG-Datei erstellen
- **`image`-Feld in JEDER Section** — `image: "images/section-[nr]-[kurzname].svg"` + SVG-Datei erstellen. Jede Section braucht ein eigenes Bild!
- **`labels` auf JEDEM Example** — mindestens 1 Label pro Example (wird als Badge auf der Karte angezeigt)
- **`rel` auf JEDEM Example** (wo inhaltlich sinnvoll) — wird als Learning Item mit Fortschrittsbalken angezeigt

### Bilder und Medien
- **Lesson-Bild (PFLICHT):** `image: "images/lesson-header.svg"` auf Top-Level — wird als Thumbnail auf der Lektionskarte angezeigt. Ohne Bild sieht die Karte leer aus.
- **Section-Bild (PFLICHT):** `image` + `image_caption` auf JEDER Section — über der Erklärung. Jede Section braucht eine passende Illustration, die den Inhalt visuell zusammenfasst. Ohne Bild wirkt die Lektion trocken und ist schwerer zu merken.
- **Example-Bild (optional):** `image` + `image_caption` auf Example-Ebene — Thumbnail neben der Frage
- **Video (optional):** `video`-Feld auf Section-Ebene (YouTube/Vimeo-URL) — wird als Embed angezeigt

### Coach-Konfiguration (optional)
Falls gewünscht, in `workshops.yaml` ergänzen:
```yaml
coach:
  email: "coach@example.com"
  name: "Coach-Name"
```
Aktiviert den "Ergebnisse per E-Mail senden"-Button auf der Assessment-Seite.

### Labels systematisch einsetzen (PFLICHT)
- **Jedes Example braucht mindestens 1 Label** — Labels werden als Badges auf der Lektionskarte und als Filter in der App angezeigt
- Für Sprachen: Grammatik-Konzepte (`Präsens`, `Futur`, `Passiv`, `Konjunktiv`, etc.)
- Für IT/Code: Kategorien (`Basics`, `Netzwerk`, `Dateisystem`, `Prozesse`, `Konfiguration`, etc.)
- Für Wissens-Themen: Schwierigkeitsgrad oder Unterthema
- Labels konsistent über alle Lektionen verwenden
- Maximal 3–4 verschiedene Labels pro Lektion (zu viele Labels = unübersichtlich)

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

## Story Mode Workshops

Story-Workshops sind interaktive Geschichten mit Entscheidungen und Verzweigungen — kein klassischer Lernpfad, sondern ein narratives Erlebnis (z.B. `workshop-milas-abenteuer`).

### Struktur

Gleiche Ordnerstruktur wie Lern-Workshops, aber `content.yaml` nutzt andere Felder.

### workshops.yaml (Story Mode)

```yaml
workshops:
  - folder: [story-name]
    code: de-DE
    title: "[Titel]"
    description: "[1-2 Sätze Beschreibung]"
    color: "[H S% L%]"
    primaryColor: "[H S% L%]"
    image: "[story-name]/thumbnail.svg"
    labels: ["[Genre]", "[Zielgruppe]"]   # Workshop-Ebene: Genres, Altersgruppen
```

### content.yaml Schema (Story Mode)

```yaml
number: [N]
title: "[Kapitel-/Szenen-Titel]"
description: "[Ein Satz was in dieser Szene passiert]"
image: "images/[szene].svg"

sections:
  - title: "[Szenen-Abschnitt]"
    image: "images/[bild].svg"
    examples:

      # --- NARRATION ---
      # voice: narrator = Erzählerstimme
      # voice: [charakter] = Figur spricht (z.B. fridolin, grandma, mila)
      - q: "[Text der erzählt oder gesprochen wird]"
        voice: narrator

      - q: "'Dialog-Text hier,' sagte [Charakter]."
        voice: [charakter-name]

      # --- ENTSCHEIDUNG (Branching ohne richtig/falsch) ---
      # goto auf Option-Ebene: Leser wählt Weg, geht zu Lektion+Section
      - q: "Wohin soll [Charakter] gehen?"
        type: select
        options:
          - text: "Option A"
            image: "images/option-a.svg"       # optional: Bild pro Option
            goto: { lesson: 2, section: 0 }    # Springe zu Lektion 2, Section 0
          - text: "Option B"
            image: "images/option-b.svg"
            goto: { lesson: 3, section: 0 }

      # --- QUIZ MIT BEDINGTER WEITERLEITUNG ---
      # goto_correct / goto_wrong auf Frage-Ebene
      # Typ: select (eine richtige Antwort)
      - q: "Wie heißt der Frosch?"
        type: select
        goto_correct: { lesson: 2, section: 2 }    # Bei richtiger Antwort
        goto_wrong: { lesson: 2, section: 1 }       # Bei falscher Antwort
        options:
          - text: "Fridolin"
            correct: true
          - text: "Ferdinand"
          - text: "Friedrich"

      # Typ: multiple-choice (mehrere richtige Antworten)
      - q: "Was hat Mila am Fluss gesehen?"
        type: multiple-choice
        goto_correct: { lesson: 2, section: 3 }
        goto_wrong: { lesson: 2, section: 2 }
        options:
          - text: "Einen Frosch"
            correct: true
          - text: "Einen Drachen"
          - text: "Einen Fluss"
            correct: true
          - text: "Ein Einhorn"

      # Typ: input mit mehreren akzeptierten Antworten
      # answers[] statt a: — jede Antwort hat eigenes goto
      - q: "Was hatte die alte Frau?"
        type: input
        answers:
          - text: "ein Buch"
            goto: { lesson: 3, section: 2 }
          - text: "Buch"
            goto: { lesson: 3, section: 2 }
          - text: "ein großes Buch"
            goto: { lesson: 3, section: 2 }
        goto_wrong: { lesson: 3, section: 1 }    # Bei keiner Übereinstimmung
```

### goto-Referenz

| Feld | Wo | Bedeutung |
|------|----|-----------|
| `goto` | auf `option` | Bei Auswahl dieser Option — springt zu `{ lesson: N, section: N }` |
| `goto_correct` | auf `example` | Bei richtiger Antwort (select, multiple-choice, input) |
| `goto_wrong` | auf `example` | Bei falscher Antwort |
| `voice` | auf `example` | Sprecherin: `narrator` oder Figuren-Name (z.B. `fridolin`, `grandma`) |

**lesson** = 1-basierter Index der Lektion (wie in `lessons.yaml`)
**section** = 0-basierter Index der Section innerhalb der Lektion

### Navigation im Story Mode (UX)

Der Story Mode hat zwei Navigationsebenen — berücksichtige das beim Schreiben:

**Links/Rechts-Tippen (egal wo auf dem Bildschirm):**
- Rechts → nächster Absatz (Example) innerhalb der aktuellen Section — Audio springt mit
- Links → vorheriger Absatz innerhalb der aktuellen Section
- Section wechselt automatisch nach dem letzten Absatz

**„zurück / weiter" Buttons (unten im Buch):**
- Wechseln Seiten innerhalb einer Section, wenn eine Section mehr als ~5 Absätze hat

**Konsequenz fürs Schreiben:**
- Jede Section sollte **2–6 Absätze** haben — dann ist eine Section = eine Bildschirmansicht ohne Paginierung
- Absätze sollen kurz und klar sein — sie werden einzeln vorgelesen
- Entscheidungs-Examples (type: select/multiple-choice/input) am **Ende einer Section** platzieren — nie mittendrin

### Qualitätsregeln für Story Mode

- **Kein `version: 2` Pflicht** — Story Mode hat kein Schema-Versionierungsfeld
- **Keine Pflicht-`labels` per Example** — Story Mode nutzt `labels` nur auf Workshop-Ebene
- **Keine `rel`-IDs** — Story Mode hat keine Vokabel-/Fortschrittsverfolgung
- **Jede Lektion braucht ein Bild** — `image` auf Top-Level (PFLICHT)
- **Jede Section braucht ein Bild** — `image` auf Section-Ebene (PFLICHT)
- **Branches schließen sich** — Alle Verzweigungen müssen in gemeinsamen Szenen enden (keine Sackgassen)
- **Charaktere konsistent benennen** — `voice`-Werte einmal festlegen und konsequent nutzen
- **Min. 3 Lektionen** — pro Entscheidungstiefe eine Lektion (z.B. L1 = Intro, L2 = Weg A, L3 = Weg B)
- **Entscheidungen beschriften** — Options-Text klar und eindeutig formulieren

### Bilder für Story Mode

- **Lesson-Header (PFLICHT):** 640×360 SVG — zeigt die Hauptszene der Lektion
- **Section-Bilder (PFLICHT):** 640×160 SVG — Terminal-Card-Stil oder Szenenausschnitt
- **Options-Bilder (optional):** SVG pro Auswahlmöglichkeit — macht Entscheidungen visuell greifbar
- **Charakterbilder:** Konsistente Illustration pro Figur (wird mehrfach referenziert)

### Beispiel: Milas Abenteuer

`workshop-milas-abenteuer` ist das Referenz-Beispiel für Story Mode:
- 3 Lektionen: Einleitung → Weg A (Fluss) / Weg B (Haus)
- Figuren: `narrator`, `fridolin`, `grandma`
- Entscheidungen: Freie Wahl (goto auf options) + Quiz mit goto_correct/goto_wrong
- Input-Antworten: answers[] mit individuellem goto pro Antwort

## Was du NICHT tust
- Kein Git, kein Push, kein PR
- Keine Platzhalter — jede Datei vollständig
- Nicht mehr als einen Workshop gleichzeitig
