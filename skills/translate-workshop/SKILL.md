---
name: translate-workshop
description: Add a new language translation to an existing workshop. Copies structure, translates content, and updates index.yaml.
argument-hint: <workshop-name> --lang <language-code>
allowed-tools: Read, Write, Glob, Bash, Grep
---

# Workshop Übersetzer

Füge eine neue Sprache zu einem bestehenden Workshop hinzu.

## Schritt 1 — Workshop und Sprache klären

Frage den User nach:
- **Workshop:** Welcher Workshop soll übersetzt werden?
- **Zielsprache:** In welche Sprache? (z.B. english, farsi, arabic, francais)

Falls Argumente übergeben wurden (z.B. `/translate-workshop linux --lang en`), nutze diese direkt.

### Workshop finden
```
/Users/reza/Github/openlearnapp/workshops/workshop-{name}/
```
Prüfe ob der Workshop existiert und lies die bestehende Struktur.

## Schritt 2 — Quellsprache identifizieren

Lies `index.yaml` im Workshop-Ordner. Die erste (oder einzige) Sprache ist die Quellsprache.

Lies alle `content.yaml` Dateien der Quellsprache um den Inhalt zu verstehen.

## Schritt 3 — Neue Sprache anlegen

### Sprachcodes

| Sprache | Ordner | Code |
|---------|--------|------|
| Deutsch | `deutsch` | `de-DE` |
| English | `english` | `en-US` |
| Farsi | `farsi` | `fa-IR` |
| Arabisch | `arabic` | `ar-SA` |
| Französisch | `francais` | `fr-FR` |
| Spanisch | `espanol` | `es-ES` |
| Portugiesisch | `portugiesisch` | `pt-PT` |

### Ordnerstruktur erstellen

```
workshop-{name}/
├── [neue-sprache]/
│   ├── workshops.yaml
│   └── [thema-übersetzt]/
│       ├── lessons.yaml
│       ├── thumbnail.svg        # Kopie mit übersetztem Text
│       ├── 01-[titel]/
│       │   ├── content.yaml
│       │   └── images/
│       │       └── lesson-header.svg   # Kopie aus Quellsprache
│       └── N-[titel]/
│           ├── content.yaml
│           └── images/
│               └── lesson-header.svg
```

### index.yaml erweitern

Füge die neue Sprache zu `index.yaml` hinzu:
```yaml
languages:
  - folder: deutsch          # bestehend
    code: de-DE
  - folder: english          # NEU
    code: en-US
```

## Schritt 4 — Inhalte übersetzen

### Was wird übersetzt:
| Feld | Übersetzen? | Beispiel |
|------|-------------|---------|
| `title` (Lektion) | ✅ Ja | "Grundlagen" → "Basics" |
| `description` | ✅ Ja | Beschreibungstext |
| `explanation` | ✅ Ja | Erklärungen und Markdown |
| `a` (Antwort) | ✅ Ja | Antworten/Erklärungen |
| Section `title` | ✅ Ja | Abschnittstitel |
| `options[].text` | ✅ Ja | Assessment-Optionen |
| `image_caption` | ✅ Ja | Bildunterschriften |

### Was bleibt gleich:
| Feld | Übersetzen? | Warum |
|------|-------------|-------|
| `q` (Frage) | ❌ Nein | Lerninhalt ist sprachunabhängig |
| `rel` IDs | ❌ Nein | Erster Wert = globaler Identifier |
| `rel` Bedeutung (2. Wert) | ✅ Ja | Übersetzung der Bedeutung |
| `type` | ❌ Nein | Technisches Feld |
| `correct` | ❌ Nein | Technisches Feld |
| `labels` | ❌ Nein | Konsistente Kategorien |
| `version`, `number` | ❌ Nein | Technische Felder |
| `video` | ❌ Nein | Gleiche URL |
| `image` (Pfad) | ❌ Nein | Gleicher relativer Pfad |
| `image_caption` | ✅ Ja | Bildunterschrift übersetzen |

### Lesson-Bilder kopieren
- Kopiere den `images/` Ordner aus der Quellsprache in jede Lektion der Zielsprache
- SVG-Bilder enthalten meist keine sprachspezifischen Texte → 1:1 kopieren
- Falls ein SVG sichtbaren Text in der Quellsprache enthält → Text übersetzen

### Sonderfall Sprach-Workshops
Bei Sprach-Workshops (z.B. Portugiesisch lernen) werden `q`-Felder kopiert, aber die `a`-Felder in die Zielsprache übersetzt:
- Deutsch: `q: "Olá"` → `a: "Hallo"`
- English: `q: "Olá"` → `a: "Hello"`

### Sonderfall Nicht-Sprach-Workshops
Bei IT/Code/Wissens-Workshops werden `q`-Felder ebenfalls übersetzt, da der Lerninhalt sprachabhängig ist:
- Deutsch: `q: "Was macht der Befehl ls?"` → `a: "Zeigt Dateien an"`
- English: `q: "What does the ls command do?"` → `a: "Lists files"`

## Schritt 5 — workshops.yaml erstellen

```yaml
workshops:
  - folder: [thema-übersetzt]
    code: [sprach-code]
    title: "[Übersetzter Anzeigename]"
    description: "[Übersetzte Beschreibung]"
    color: "[gleiche Farbe wie Quellsprache]"
    primaryColor: "[gleiche primaryColor wie Quellsprache]"
    image: "[thema-übersetzt]/thumbnail.svg"
```

Farben und Coach-Konfiguration von der Quellsprache übernehmen.

## Schritt 6 — Übergabe

Zeige dem User:
```
✅ Übersetzung [Sprache] für Workshop [Name] fertig!
📁 workshop-{name}/[sprache]/
📚 [N] Lektionen übersetzt
🌐 Workshop hat jetzt [M] Sprachen: [liste]

→ /publish-workshop [name]  Workshop mit allen Sprachen veröffentlichen
```

## Was du NICHT tust
- Keine `rel`-IDs ändern (müssen über Sprachen hinweg konsistent bleiben)
- Keine Lektionen hinzufügen oder entfernen
- Keine Reihenfolge ändern
- Keine Labels übersetzen (bleiben als einheitliche Kategorien)
