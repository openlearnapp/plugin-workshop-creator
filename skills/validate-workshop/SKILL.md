---
name: validate-workshop
description: Validate a workshop against the OpenLearn YAML schema. Checks file structure, YAML syntax, rel-ID uniqueness, language codes, and assessment format.
argument-hint: [workshop-name]
allowed-tools: Read, Bash, Glob, Grep
---

# Workshop Validator

Prüfe einen Workshop gegen das OpenLearn-Schema und melde Fehler.

## Schritt 1 — Workshop finden

Falls ein Argument übergeben wurde, nutze es als Workshop-Name:
```
/Users/reza/Github/openlearnapp/workshops/workshop-{name}/
```

Falls kein Argument: liste alle Workshops und frage den User.

## Schritt 2 — Validierung durchführen

Führe **alle** Prüfungen durch und sammle die Ergebnisse.

### a) Dateistruktur

- [ ] `index.yaml` existiert im Workshop-Root
- [ ] Für jede Sprache in `index.yaml`:
  - [ ] Sprach-Ordner existiert
  - [ ] `workshops.yaml` existiert im Sprach-Ordner
  - [ ] Workshop-Ordner existiert (wie in `workshops.yaml` referenziert)
  - [ ] `lessons.yaml` existiert im Workshop-Ordner
  - [ ] Alle in `lessons.yaml` gelisteten Ordner existieren
  - [ ] Jeder Lektion-Ordner enthält `content.yaml`
- [ ] `CONTRIBUTING.md` existiert
- [ ] `index.html` existiert

### b) YAML-Syntax

Für **jede** YAML-Datei prüfen:
```bash
python3 -c "import yaml; yaml.safe_load(open('DATEI'))" 2>&1
```
Falls `python3` nicht verfügbar:
```bash
yq '.' DATEI > /dev/null 2>&1
```

### c) Schema-Validierung (content.yaml)

Für jede `content.yaml`:
- [ ] `version` ist `2`
- [ ] `number` ist eine Ganzzahl
- [ ] `title` ist vorhanden und nicht leer
- [ ] `description` ist vorhanden und nicht leer
- [ ] `sections` ist ein Array mit mindestens 1 Eintrag
- [ ] Jede Section hat `title`
- [ ] Jedes Example hat `q` und `a` (außer bei select/multiple-choice ohne `a`)
- [ ] Assessments (type: select/multiple-choice) nutzen `correct: true` Marker:
  ```yaml
  # RICHTIG
  options:
    - text: "Option"
      correct: true
  # FALSCH (veraltet)
  options:
    - "Option"
  a: "Option"
  ```

### d) rel-ID Eindeutigkeit

Sammle alle `rel`-IDs (erster Wert jedes rel-Arrays) über **alle** Lektionen einer Sprache:
- [ ] Keine doppelten rel-IDs innerhalb des Workshops
- Melde: welche IDs doppelt sind und in welchen Dateien

### e) Sprachcodes

- [ ] Alle `code`-Felder in `index.yaml` und `workshops.yaml` sind gültige BCP 47 Codes
- Gültige Codes: `de-DE`, `en-US`, `fa-IR`, `ar-SA`, `pt-PT`, `es-ES`, `fr-FR`, `ja-JP`, `zh-CN`, `ko-KR`, `it-IT`, `ru-RU`, `tr-TR`, `nl-NL`, `pl-PL`, `sv-SE`

### f) Konsistenz über Sprachen

Falls mehrere Sprachen vorhanden:
- [ ] Gleiche Anzahl Lektionen in jeder Sprache
- [ ] Gleiche Lektionsnummern (`number`-Feld) in jeder Sprache
- [ ] Gleiche `rel`-IDs über Sprachen hinweg (erster Wert)

### g) Deployment-Bereitschaft

- [ ] `.gitignore` existiert und enthält `.DS_Store`
- [ ] Keine `.DS_Store` Dateien im Workshop-Ordner vorhanden
- [ ] `.github/workflows/static.yml` existiert (GitHub Pages Deployment)
- [ ] Workshop-URL in `/Users/reza/Github/openlearnapp/openlearnapp.github.io/public/default-sources.yaml` vorhanden (Landing Page)

### h) Qualitätsprüfung

- [ ] Mindestens 10 Lektionen
- [ ] Jede Lektion hat mindestens 4 Sections
- [ ] Letzte Section jeder Lektion enthält mindestens 1 Assessment (type: input/select/multiple-choice)
- [ ] `workshops.yaml` hat `color` und `primaryColor`

## Schritt 3 — Ergebnis anzeigen

```
🔍 Validierung: Workshop [Name]

✅ Dateistruktur          [N] Sprachen, [M] Lektionen
✅ YAML-Syntax            [X] Dateien geprüft
✅ Schema (version 2)     Alle content.yaml korrekt
✅ rel-IDs                [Y] eindeutige IDs
✅ Sprachcodes            Alle gültig
✅ Sprach-Konsistenz      [N] Sprachen synchron
✅ Deployment             .gitignore ✓, Workflow ✓, Landing Page ✓
✅ Qualität               [M] Lektionen, [S] Sections/Lektion

Ergebnis: ✅ Workshop ist bereit zum Veröffentlichen
```

Bei Fehlern:
```
🔍 Validierung: Workshop [Name]

✅ Dateistruktur          2 Sprachen, 10 Lektionen
❌ YAML-Syntax            Fehler in deutsch/linux/05-netzwerk/content.yaml:42
✅ Schema (version 2)     Alle content.yaml korrekt
⚠️  rel-IDs               3 doppelte IDs: "ls", "cd", "pwd"
✅ Sprachcodes            Alle gültig
❌ Sprach-Konsistenz      deutsch: 10 Lektionen, english: 8 Lektionen
❌ Deployment             .DS_Store gefunden, kein Workflow, nicht in Landing Page
✅ Qualität               10 Lektionen, 5 Sections/Lektion

Ergebnis: ❌ 2 Fehler, 1 Warnung — bitte beheben vor /publish-workshop
```

## Was du NICHT tust
- Keine Dateien verändern — nur lesen und berichten
- Keine Vorschläge zur Inhaltverbesserung (nur Schema/Struktur)
