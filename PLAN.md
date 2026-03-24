# Plugin Workshop Creator — Verbesserungsplan

Abgleich des Plugins mit der OpenLearn-Plattform-Dokumentation.

## Offene Punkte

### 1. YAML-Validierung nach Erstellung

Die Plattform-Doku listet häufige Fehler auf (bad indentation, fehlende content.yaml, falsche Folder-Referenzen, ungültige Sprachcodes). Das Plugin hat keine Validierung.

**Was fehlt:**
- Nach Erstellung alle YAML-Dateien auf Syntax prüfen
- Prüfen ob alle in `lessons.yaml` gelisteten Ordner existieren und `content.yaml` enthalten
- Prüfen ob Sprachcodes gültige BCP 47 Codes sind
- Prüfen ob `rel`-IDs tatsächlich eindeutig sind
- Könnte ein eigener `/validate-workshop` Skill werden

**Aufwand:** Mittel

## Erledigte Punkte

- ~~Schema-Version auf 2~~ (v1.1.0)
- ~~Assessment-Format auf correct-Marker~~ (v1.1.0)
- ~~GitHub Pages Workflow in publish-workshop~~ (v1.1.0)
- ~~Sections-Anzahl auf 4-6 erhöht~~ (v1.1.0)
- ~~Coach-Konfiguration dokumentiert~~ (v1.1.0)
- ~~Labels systematisch dokumentiert~~ (v1.1.0)
- ~~Bilder/Video-Support dokumentiert~~ (v1.1.0)
- ~~Video-Regel korrigiert~~ (v1.2.0)
- ~~Default-Sources URL-Format korrigiert~~ (v1.2.0)
- ~~Audio-Hinweis in Übergabe~~ (v1.2.0)
- ~~Workshop-Creator auf 1 Sprache umgestellt~~ (v1.2.0)
- ~~/translate-workshop Skill erstellt~~ (v1.2.0)
- ~~/extend-workshop Skill erstellt~~ (v1.2.0)
- ~~CONTRIBUTING.md Template~~ (v1.2.0)
- ~~import-workshop → publish-workshop umbenannt~~ (v1.2.0)
