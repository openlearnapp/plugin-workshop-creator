---
name: extend-workshop
description: Add more lessons to an existing workshop. Extends lessons.yaml and creates new content files without touching existing lessons.
argument-hint: <workshop-name> [--lessons 11-20]
allowed-tools: Read, Write, Glob, Bash, Grep
---

# Workshop Erweitern

Füge weitere Lektionen zu einem bestehenden Workshop hinzu.

## Schritt 1 — Workshop und Umfang klären

Frage den User nach:
- **Workshop:** Welcher Workshop soll erweitert werden?
- **Lektionen:** Welche Lektionsnummern? (z.B. 11-20, oder "5 weitere")
- **Themen:** Was soll in den neuen Lektionen behandelt werden?

Falls Argumente übergeben wurden (z.B. `/extend-workshop linux --lessons 11-20`), nutze diese direkt.

### Workshop finden
```
/Users/reza/Github/openlearnapp/workshops/workshop-{name}/
```

## Schritt 2 — Bestehenden Workshop analysieren

**Pflicht-Analyse vor der Erstellung:**

1. Lies `index.yaml` — welche Sprachen existieren?
2. Lies alle bestehenden `content.yaml` Dateien:
   - Welche Themen wurden bereits behandelt?
   - Welche `rel`-IDs wurden bereits vergeben?
   - Welcher Schwierigkeitsgrad wurde erreicht?
   - Welcher narrative Faden läuft durch die Lektionen?
3. Lies `lessons.yaml` — welche Lektionsnummern existieren?

**Sammle alle bestehenden `rel`-IDs** um Duplikate zu vermeiden.

## Schritt 3 — Neue Lektionen erstellen

### Für jede Sprache im Workshop:

1. Erstelle neue Lektion-Ordner: `{N+1}-[titel]/content.yaml`
2. Erstelle `images/lesson-header.svg` in jedem neuen Lektion-Ordner mit dem **Write-Tool** (640×360, 16:9, flat design, thematisch passend zur Lektion)
3. Erweitere `lessons.yaml` um die neuen Einträge (bestehende NICHT ändern)
4. Halte die gleichen Qualitätsregeln ein wie `/workshop-creator`:
   - `version: 2`
   - 4–6 Sections, 3–5 Examples pro Section
   - Letzte Section = Wissens-Check mit `correct: true` Markern
   - Neue `rel`-IDs die nicht mit bestehenden kollidieren
   - `description`-Feld in jeder neuen Lektion
   - `image: "images/lesson-header.svg"` in jeder neuen Lektion (PFLICHT)
   - `image: "images/section-[nr]-[kurzname].svg"` in jeder Section (PFLICHT — 640×160 SVG)
   - `labels` auf jedem Example (PFLICHT — mindestens 1 Label)
   - `rel` auf jedem Example (wo inhaltlich sinnvoll)

### Inhaltliche Anknüpfung

- **Progressive Difficulty:** Neue Lektionen bauen auf den bestehenden auf
- **Interleaving:** Themen aus früheren Lektionen in neuen Beispielen referenzieren
- **Narrative Verankerung:** Den bestehenden roten Faden fortführen
- **Review:** Bei jeder 5. Lektion eine Review-Lektion einplanen

### Für Sprach-Workshops: Phasen beachten

| Phase | Lektionen | Fokus |
|-------|-----------|-------|
| Phase 1 | 1–15 | Verb-Grundlagen, 50 häufigste Verben |
| Phase 2 | 16–25 | Zeiten & Grammatik |
| Phase 3 | 26–35 | Fortgeschrittene Grammatik |
| Phase 4 | 36–45 | Vokabelerweiterung nach Themen |
| Phase 5 | 46–50 | Meisterschaft & Verfeinerung |

Bestimme anhand der bestehenden Lektionsanzahl, in welcher Phase sich der Workshop befindet.

## Schritt 4 — Status aktualisieren

Aktualisiere `/Users/reza/Github/openlearnapp/workshops/workshop-status.md`:

```markdown
### ✏️ [DATUM] ERWEITERT: Workshop [Name]
- **Neue Lektionen:** [N] → [M] (z.B. 10 → 20)
- **Neue Themen:** [Stichworte]
- **Sprachen aktualisiert:** [liste]
- **Status:** BEREIT ZUM IMPORTIEREN
```

## Schritt 5 — Übergabe

Zeige dem User:
```
✅ Workshop [Name] erweitert!
📁 workshop-{name}/
📚 [N] → [M] Lektionen, ca. [X] neue Beispiele
🔗 Bestehende rel-IDs: [Anzahl] | Neue rel-IDs: [Anzahl]

→ /publish-workshop [name]  Aktualisierte Version veröffentlichen
```

## Was du NICHT tust
- Bestehende Lektionen NICHT verändern
- Bestehende `rel`-IDs NICHT wiederverwenden
- `lessons.yaml` nur erweitern, NICHT überschreiben
- Keine Lektionsnummern bestehender Lektionen ändern
