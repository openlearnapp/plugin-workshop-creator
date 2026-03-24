---
name: import-workshop
description: Import a finished workshop from the local staging folder into a GitHub repo and make it live on open-learn.app
argument-hint: [workshop-name]
allowed-tools: Read, Write, Bash, Glob, Grep
---

# Workshop Importer

Importiere einen fertigen Workshop aus `/Users/reza/Github/openlearnapp/workshops/` ins Projekt.

## Ablauf

1. Lies `/Users/reza/Github/openlearnapp/workshops/workshop-status.md`
2. Finde Workshops mit Status `BEREIT ZUM IMPORTIEREN`
3. Falls ein Argument übergeben wurde, importiere nur diesen Workshop

## Für jeden Workshop:

### a) GitHub-Repo prüfen/erstellen
```bash
gh repo view openlearnapp/workshop-{name} 2>/dev/null
# Falls nicht gefunden:
gh repo create openlearnapp/workshop-{name} --public --description "Open Learn Workshop: ..."
```

### b) Dateien kopieren und pushen
```bash
cd /Users/reza/Github/openlearnapp/workshops/workshop-{name}
git init && git remote add origin https://github.com/openlearnapp/workshop-{name}.git
git add -A && git commit -m "feat: {name} workshop — {N} lessons, {languages}"
git branch -M main && git push -u origin main
```

### c) Default-Sources aktualisieren
Prüfe ob die URL bereits in `public/default-sources.yaml` steht:
```yaml
- https://open-learn.app/workshop-{name}/index.yaml
```
Falls nicht: hinzufügen, committen, PR erstellen.

### d) Status aktualisieren
In `workshop-status.md`: Status → `✅ Im Projekt`

## Regeln
- NIEMALS ohne Reza's OK mergen
- Immer zeigen was gemacht wird
- GitHub Pages braucht 1-2 Minuten nach Push
