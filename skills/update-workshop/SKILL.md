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
- [ ] `workshops.yaml` hat `image`-Feld bei jedem Workshop-Eintrag (z.B. `image: "[thema]/thumbnail.svg"`) — ohne `image` kein Bild auf der Übersichtsseite

### c) Qualitäts-Check
- [ ] Mindestens 4 Sections pro Lektion
- [ ] 3–5 Examples pro Section
- [ ] Letzte Section = Wissens-Check mit Assessments
- [ ] `rel`-IDs eindeutig über gesamten Workshop
- [ ] Labels konsistent verwendet

### d) Pflichtfelder-Check (neue Anforderungen)
- [ ] **`image`-Feld in jeder Lektion** — `image: "images/lesson-header.svg"` + SVG existiert
- [ ] **`labels` auf jedem Example** — mindestens 1 Label pro Example
- [ ] `thumbnail.svg` existiert pro Sprache/Workshop
- [ ] `terminal-sim.yaml` vorhanden (für IT/Code-Workshops)
- [ ] `rel` auf Examples gesetzt (wo inhaltlich sinnvoll)

## Schritt 3 — Befund anzeigen

Zeige dem User eine Übersicht:

```
🔄 Workshop-Update: [Name]

📋 Befund:
✅ index.yaml                    korrekt
❌ README.md                     Landing Page + Start Workshop Links fehlen
✅ CONTRIBUTING.md               vorhanden
⚠️  thumbnail.svg                deutsch/ vorhanden, english/ fehlt
❌ Workshop-Bilder               farsi/workshops.yaml: image-Feld fehlt
❌ Lesson-Bilder                 0 von 12 (PFLICHT — ohne Bild sieht Karte leer aus)
✅ Schema version 2              alle 12 Lektionen
❌ Labels                        nur in 4 von 12 Lektionen (PFLICHT auf jedem Example)
❌ terminal-sim.yaml             0 von 12 (IT-Workshop → empfohlen)
✅ Assessments                   correct: true Format

📝 Geplante Änderungen:
1. README.md — Links ergänzen
2. english/docker/thumbnail.svg — erstellen (Kopie mit EN-Text)
3. Lesson-Bilder — 12× SVG in images/ generieren (PFLICHT)
4. Labels — auf allen Examples ergänzen (PFLICHT)
5. terminal-sim.yaml — für 10 Lektionen erstellen

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

### 4c) workshops.yaml: image-Feld ergänzen

Falls `workshops.yaml` in einer Sprache kein `image`-Feld hat:
- Ergänze `image: "[thema]/thumbnail.svg"` im Workshop-Eintrag
- Prüfe ob die referenzierte `thumbnail.svg` existiert (falls nicht → 4d erstellt sie)

### 4d) Thumbnails ergänzen (thumbnail.svg)

Falls `thumbnail.svg` für eine Sprache fehlt:
- Kopiere vom bestehenden Thumbnail
- Übersetze den Titel-Text in die Zielsprache
- Behalte Farben und Layout

### 4d) Lesson-Bilder generieren (PFLICHT für alle Workshops)

**Jede Lektion braucht ein Bild.** Ohne Bild sieht die Lektionskarte im neuen Lernpfad-Layout leer aus.

Für jede Lektion ohne `image`-Feld:

1. **Bash:** `mkdir -p [lektion]/images/`
2. **Write-Tool:** Schreibe die SVG-Datei nach `[lektion]/images/lesson-header.svg` (640×360, 16:9, flat design, thematisch passend)
3. **Edit-Tool:** Setze `image: "images/lesson-header.svg"` in `content.yaml`
4. **Edit-Tool:** Setze `image_caption: "[Beschreibung]"` in `content.yaml`

**Wichtig:** Die SVG-Datei muss tatsächlich mit dem Write-Tool erstellt werden — nicht nur der Pfad in content.yaml!

### 4e) Section-Bilder generieren (PFLICHT für alle Sections)

**Jede Section braucht ein Bild.** Section-Bilder machen Lektionen visuell ansprechend und merkbar.

Für jede Section ohne `image`-Feld:

1. **Write-Tool:** Schreibe die SVG-Datei nach `[lektion]/images/section-[nr]-[kurzname].svg` (640×160, breit/Banner-artig, flat design, thematisch passend zum Section-Inhalt)
2. **Edit-Tool:** Setze `image: "images/section-[nr]-[kurzname].svg"` in `content.yaml` auf der Section
3. **Edit-Tool:** Setze `image_caption: "[Beschreibung]"` auf der Section

**Bild-Inhalt je nach Workshop-Typ:**
- **IT/Code:** Architektur-Diagramme, Terminal-Darstellungen, Netzwerk-Topologien
- **Sprachen:** Thematische Szenen (Café, Marktplatz, Reise), Flaggen, Sprechblasen
- **Wissenschaft:** Schematische Darstellungen, Prozessdiagramme
- **Geschichte:** Zeitleisten, Karten, Gebäude-Silhouetten

Das Bild soll das **Kernkonzept der Lektion** visuell darstellen — nicht nur ein generischer Hintergrund mit Text.

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

### 4f) Labels ergänzen (PFLICHT auf jedem Example)

**Jedes Example braucht mindestens 1 Label.** Labels werden als Badges auf der Lektionskarte und als Filter in der App angezeigt.

Für jedes Example ohne `labels`:
- Analysiere den Inhalt (`q`, `a`, Section-Kontext)
- Setze 1–2 passende Labels

**Label-Katalog nach Workshop-Typ:**
- IT: `Basics`, `Netzwerk`, `Dateisystem`, `Prozesse`, `Konfiguration`, `Sicherheit`, `Scripting`
- Sprachen: `Präsens`, `Futur`, `Passiv`, `Substantiv`, `Verb`, `Adjektiv`, `Konversation`
- Wissenschaft: `Theorie`, `Experiment`, `Formel`, `Anwendung`
- Geschichte: `Ereignis`, `Person`, `Epoche`, `Kultur`

**Regeln:**
- Bestehende Labels nie ändern oder entfernen
- Maximal 3–4 verschiedene Labels pro Lektion
- Labels konsistent über alle Lektionen verwenden

## Schritt 5 — Ergebnis

```
✅ Workshop [Name] aktualisiert!

Änderungen:
- README.md: Links ergänzt
- 2× thumbnail.svg erstellt
- 12× Lesson-Bilder in images/ generiert
- Labels auf [X] Examples ergänzt
- 10× terminal-sim.yaml erstellt

Texte, Erklärungen und Beispiele bleiben unberührt.

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
