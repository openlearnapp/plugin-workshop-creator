# Plugin Workshop Creator — Regeln

## Workflow

- **Immer PRs:** Nie direkt auf `main` pushen. Für jede Änderung einen Branch erstellen und einen PR öffnen.
- **Immer Version bumpen:** Bei jedem PR die Version in **beiden** Dateien hochsetzen:
  - `.claude-plugin/plugin.json`
  - `.claude-plugin/marketplace.json`
- **Immer CHANGELOG.md pflegen:** Für jeden Commit / PR einen Eintrag in `CHANGELOG.md` hinzufügen.
- **Immer Docs aktualisieren:** Bei neuen/geänderten Skills auch `README.md` aktualisieren (Skill-Tabelle, Workflow-Diagramm, Strukturbaum).

## Versionierung

Semver: `MAJOR.MINOR.PATCH`
- **PATCH:** Bugfixes, kleine Korrekturen
- **MINOR:** Neue Features, neue Skills, Erweiterungen
- **MAJOR:** Breaking Changes
