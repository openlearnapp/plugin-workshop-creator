---
name: update-workshop
description: Idempotent update of an existing workshop — fixes structural issues, migrates schema, adds missing files (README links, thumbnails, images), and applies current quality standards. Safe to run multiple times.
argument-hint: <workshop-name> [--dry-run]
allowed-tools: Read, Write, Glob, Bash, Grep
---

# Workshop Updater (Idempotent)

Bringe einen bestehenden Workshop auf den aktuellen Stand. Kann beliebig oft ausgeführt werden — ändert nur was nötig ist, lässt Bestehendes intakt.

## Wann verwenden?

- Workshop wurde mit einer älteren Plugin-Version erstellt
- Schema-Version hat sich geändert (z.B. v1 → v2)
- Neue Features verfügbar (Bilder, terminal-sim, Labels)
- Strukturelle Probleme beheben (fehlende README-Links, fehlende Thumbnails)
- Nach manuellem Editing zur Qualitätssicherung
- Regelmäßig als "Health Check" auf bestehende Workshops

## Schritt 1 — Workshop finden

Falls ein Argument übergeben wurde, nutze es als Workshop-Name. Suche in dieser Reihenfolge:
1. `/Users/reza/Github/openlearnapp/workshops/workshop-{name}/` (Staging)
2. `/Users/reza/Github/openlearnapp/workshop-{name}/` (bereits importiert)

Falls kein Argument: liste alle Workshops und frage den User.

Falls `--dry-run` angegeben: nur analysieren und berichten, nichts ändern.

## Schritt 2 — Vollständige Analyse

Lies den gesamten Workshop und erstelle einen Befund:

### a) Struktur-Check
- [ ] `index.yaml` vorhanden und korrekt
- [ ] `index.html` vorhanden mit korrektem Redirect-URL
- [ ] `README.md` vorhanden mit Landing Page + Start Workshop Links
- [ ] `CONTRIBUTING.md` vorhanden
- [ ] `.gitignore` vorhanden
- [ ] `.github/workflows/static.yml` vorhanden
- [ ] Für jede Sprache: `workshops.yaml`, `lessons.yaml`, `thumbnail.svg`
- [ ] Alle Lesson-Ordner mit `content.yaml`

### b) Schema-Check
- [ ] Alle `content.yaml` haben `version: 2`
- [ ] Alle haben `description`-Feld (nicht leer)
- [ ] Assessments nutzen `correct: true` Marker (nicht veraltetes Format)
- [ ] `workshops.yaml` hat `color` und `primaryColor`

### c) Qualitäts-Check
- [ ] Mindestens 4 Sections pro Lektion
- [ ] 3–5 Examples pro Section
- [ ] Letzte Section = Wissens-Check mit Assessments
- [ ] `rel`-IDs eindeutig über gesamten Workshop
- [ ] Labels konsistent verwendet

### d) Feature-Check (neue Features die fehlen könnten)
- [ ] `thumbnail.svg` existiert pro Sprache/Workshop
- [ ] Lesson-Bilder vorhanden (für IT/Wissenschaft/visuelle Themen)
- [ ] `terminal-sim.yaml` vorhanden (für IT/Code-Workshops)
- [ ] Labels auf Examples gesetzt

## Schritt 3 — Befund anzeigen

Zeige dem User eine Übersicht:

```
🔄 Workshop-Update: [Name]

📋 Befund:
✅ index.yaml                    korrekt
❌ README.md                     Landing Page + Start Workshop Links fehlen
✅ CONTRIBUTING.md               vorhanden
⚠️  thumbnail.svg                deutsch/ vorhanden, english/ fehlt
❌ Lesson-Bilder                 0 von 12 (IT-Workshop → empfohlen)
✅ Schema version 2              alle 12 Lektionen
❌ terminal-sim.yaml             0 von 12 (IT-Workshop → empfohlen)
✅ Assessments                   correct: true Format
⚠️  Labels                       nur in 4 von 12 Lektionen

📝 Geplante Änderungen:
1. README.md — Links ergänzen
2. english/docker/thumbnail.svg — erstellen (Kopie mit EN-Text)
3. Lesson-Bilder — SVG-Diagramme für 8 Lektionen generieren
4. terminal-sim.yaml — für 10 Lektionen erstellen
5. Labels — für 8 Lektionen ergänzen

Fortfahren? (oder --dry-run für nur Analyse)
```

**Warte auf User-Bestätigung bevor Änderungen gemacht werden.**

## Schritt 4 — Änderungen durchführen

Führe nur die nötigen Änderungen durch. Reihenfolge:

### 4a) Fehlende Strukturdateien erstellen/reparieren

**README.md** — Wenn Links fehlen, ergänze den Link-Block:
```markdown
> **[Landing Page](https://open-learn.app/workshop-[thema]/)** · **[Start Workshop](https://open-learn.app/#/add?source=https://open-learn.app/workshop-[thema]/)**
```
Ersetze nur den fehlenden Teil, behalte bestehende Inhalte.

**index.html** — Prüfe ob Redirect-URL korrekt ist:
```
https://open-learn.app/#/add?source=https://open-learn.app/workshop-[thema]/
```

**.gitignore, .github/workflows/static.yml, CONTRIBUTING.md** — Erstelle falls fehlend (Templates wie in `/workshop-creator`).

### 4b) Schema-Migration

**version: 1 → 2:**
- Setze `version: 2` in allen `content.yaml`
- Konvertiere Assessment-Format:
  ```yaml
  # ALT (v1):
  options:
    - "Option A"
    - "Option B"
  a: "Option A"

  # NEU (v2):
  options:
    - text: "Option A"
      correct: true
    - text: "Option B"
  ```

**description fehlt:**
- Generiere aus dem Lesson-Inhalt eine 1-Satz-Beschreibung
- Setze als `description`-Feld

### 4c) Thumbnails ergänzen

Falls `thumbnail.svg` für eine Sprache fehlt:
- Kopiere vom bestehenden Thumbnail
- Übersetze den Titel-Text in die Zielsprache
- Behalte Farben und Layout

### 4d) Lesson-Bilder generieren (nur für visuelle Workshops)

**Nur für IT/Code/Wissenschaft/Schritt-für-Schritt-Workshops:**

Analysiere den Lesson-Inhalt und erstelle SVG-Diagramme wo sinnvoll:
- Architektur-Diagramme (Docker-Architektur, Netzwerk-Topologie)
- Ablauf-Diagramme (Build-Prozess, Deployment-Pipeline)
- Schematische Darstellungen (Dateisystem-Baum, Prozess-Hierarchie)

Format: 960×540 SVG, clean und minimal.

Referenziere in `content.yaml`:
```yaml
image: "diagram.svg"
image_caption: "[Beschreibung]"
```

**NICHT für Sprach-Workshops** — dort sind Bilder in der Regel nicht hilfreich.

### 4e) terminal-sim.yaml generieren (nur für IT/Code-Workshops)

Für jede Lektion eines IT/Code-Workshops die Terminal-Befehle enthält:
- Extrahiere alle `q`-Felder die Befehle sind
- Erstelle simulierte Ausgaben
- Setze hilfreiche `hint`-Texte

Format:
```yaml
prompt: "user@host:~$"
commands:
  - cmd: "[Befehl]"
    output: "[Simulierte Ausgabe]"
    hint: "[Erklärung was der Befehl tut]"
```

### 4f) Labels ergänzen

Falls Labels fehlen oder inkonsistent sind:
- Analysiere bestehende Labels im Workshop
- Ergänze fehlende Labels basierend auf dem Inhalt
- IT: `Basics`, `Netzwerk`, `Dateisystem`, `Prozesse`, `Konfiguration`
- Sprachen: `Präsens`, `Futur`, `Passiv`, `Substantiv`, `Verb`
- Nur hinzufügen, nie bestehende Labels ändern

## Schritt 5 — Ergebnis

```
✅ Workshop [Name] aktualisiert!

Änderungen:
- README.md: Links ergänzt
- 2× thumbnail.svg erstellt
- 8× Lesson-Diagramme generiert
- 10× terminal-sim.yaml erstellt
- Labels in 8 Lektionen ergänzt

Keine Inhalte verändert (Texte, Erklärungen, Beispiele bleiben unberührt).

→ /validate-workshop [name]    Ergebnis prüfen
→ /publish-workshop [name]     Aktualisierte Version veröffentlichen
```

## Idempotenz-Regeln

- **Bestehende Dateien:** Nur ergänzen, nie überschreiben (außer bei Schema-Migration)
- **Bestehende Inhalte:** Texte, Erklärungen, Beispiele NIEMALS ändern
- **README:** Nur fehlende Links ergänzen, bestehenden Text behalten
- **Labels:** Nur hinzufügen, nie ändern oder entfernen
- **rel-IDs:** NIE ändern
- **Lesson-Reihenfolge:** NIE ändern
- **Wiederholte Ausführung:** Wenn alles aktuell ist, meldet der Skill "✅ Workshop ist aktuell, keine Änderungen nötig"

## Was du NICHT tust

- Bestehende Texte oder Erklärungen umschreiben
- Lektionen hinzufügen oder entfernen (dafür `/extend-workshop`)
- Übersetzungen hinzufügen (dafür `/translate-workshop`)
- Git-Operationen durchführen
- Veröffentlichen (dafür `/publish-workshop`)
