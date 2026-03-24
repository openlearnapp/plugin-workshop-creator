---
name: publish-workshop
description: Publish a finished workshop to GitHub and deploy it live on open-learn.app via GitHub Pages
argument-hint: [workshop-name]
allowed-tools: Read, Write, Bash, Glob, Grep
---

# Workshop Publisher

Veröffentliche einen fertigen Workshop aus `/Users/reza/Github/openlearnapp/workshops/` auf GitHub Pages.

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

### b) .gitignore und .DS_Store bereinigen
Vor allem anderen — sicherstellen dass keine OS-Artefakte ins Repo kommen:

1. `.gitignore` erstellen falls nicht vorhanden:
```
.DS_Store
*.swp
*~
.vscode/
.idea/
```

2. Bestehende `.DS_Store` Dateien entfernen:
```bash
find /Users/reza/Github/openlearnapp/workshops/workshop-{name} -name ".DS_Store" -delete
# Falls bereits im Git-Index:
cd /Users/reza/Github/openlearnapp/workshops/workshop-{name}
git rm --cached -r $(git ls-files -i --exclude-standard) 2>/dev/null || true
```

### c) GitHub Pages Workflow erstellen
Erstelle `.github/workflows/static.yml` im Workshop-Ordner **vor** dem ersten Commit (falls nicht bereits von `/workshop-creator` erstellt):
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

### d) Dateien committen und pushen
```bash
cd /Users/reza/Github/openlearnapp/workshops/workshop-{name}
git init && git remote add origin https://github.com/openlearnapp/workshop-{name}.git
git add -A && git commit -m "feat: {name} workshop — {N} lessons, {languages}"
git branch -M main && git push -u origin main
```

### e) GitHub Pages aktivieren
```bash
gh api repos/openlearnapp/workshop-{name}/pages -X POST -f build_type=workflow 2>/dev/null || true
```

### f) Default-Sources aktualisieren (Landing Page)
Prüfe ob die URL bereits in `/Users/reza/Github/openlearnapp/openlearnapp.github.io/public/default-sources.yaml` steht:
```yaml
- https://open-learn.app/workshop-{name}/index.yaml
```
Falls nicht vorhanden:
1. URL zur `default-sources.yaml` hinzufügen
2. Committen und pushen (oder PR erstellen)
3. **Ohne diesen Schritt erscheint der Workshop NICHT auf der Landing Page!**

### g) GitHub Issue für Pages-Einstellungen erstellen

GitHub Pages braucht manuelle Einstellungen die nur ein Repo-Admin machen kann. Erstelle ein Issue als Checkliste:

```bash
gh issue create --repo openlearnapp/workshop-{name} \
  --title "GitHub Pages einrichten" \
  --body "$(cat <<'EOF'
## GitHub Pages Einstellungen

Bitte im Repository unter **Settings → Pages** prüfen:

- [ ] **Source:** "GitHub Actions" ausgewählt (nicht "Deploy from a branch")
- [ ] **Custom domain:** `open-learn.app` eingetragen (falls gewünscht)
- [ ] **Enforce HTTPS:** aktiviert
- [ ] Erste Deployment-Action erfolgreich durchgelaufen (Actions-Tab prüfen)

### Testen

Nach Aktivierung (1-2 Minuten warten):
1. https://open-learn.app/workshop-{name}/ → sollte Workshop-Inhalte ausliefern
2. https://open-learn.app/#/add?source=https://open-learn.app/workshop-{name}/ → Workshop wird geladen

### Falls Custom Domain gewünscht
In `default-sources.yaml` der Hauptapp die URL anpassen:
`https://open-learn.app/workshop-{name}/index.yaml`
EOF
)"
```

### h) Status aktualisieren
In `workshop-status.md`: Status → `✅ Im Projekt`

## Regeln
- NIEMALS ohne Reza's OK mergen
- Immer zeigen was gemacht wird
- GitHub Pages braucht 1-2 Minuten nach Push
- Issue für Pages-Einstellungen wird automatisch erstellt
